---
title: Proveedores de directivas de autorización personalizados en ASP.NET Core
author: mjrousos
description: Obtenga información sobre cómo usar un IAuthorizationPolicyProvider personalizado en una aplicación ASP.NET Core para generar dinámicamente directivas de autorización.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 724b1f065e83302137d920fe4e0e2b381be505b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022139"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Proveedores de directivas de autorización personalizada mediante IAuthorizationPolicyProvider en ASP.NET Core 

Por [Mike Rousos](https://github.com/mjrousos)

Normalmente, cuando se usa [la autorización basada en directivas](xref:security/authorization/policies), las directivas se registran llamando `AuthorizationOptions.AddPolicy` como parte de la configuración del servicio de autorización. En algunos escenarios, puede que no sea posible (o deseable) registrar todas las directivas de autorización de esta manera. En esos casos, puede [usar un personalizado `IAuthorizationPolicyProvider` ](#ci) para controlar cómo se proporcionan las directivas de autorización.

Algunos ejemplos de escenarios en los que un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizado puede ser útil son:

* Uso de un servicio externo para proporcionar evaluación de directivas.
* Usar una gran variedad de directivas (por ejemplo, para diferentes números de sala o edades), por lo que no tiene sentido agregar cada directiva de autorización individual con una `AuthorizationOptions.AddPolicy` llamada.
* Crear directivas en tiempo de ejecución en función de la información de un origen de datos externo (por ejemplo, una base de datos) o de determinar dinámicamente los requisitos de autorización mediante otro mecanismo.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) del [repositorio de github de AspNetCore](https://github.com/dotnet/AspNetCore). Descargue el archivo ZIP del repositorio dotnet/AspNetCore. Descomprima el archivo. Navegue a la carpeta de proyecto *src/Security/samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Personalizar la recuperación de directivas

ASP.NET Core aplicaciones usan una implementación de la `IAuthorizationPolicyProvider` interfaz para recuperar las directivas de autorización. De forma predeterminada, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) se registra y se usa. `DefaultAuthorizationPolicyProvider`Devuelve las directivas del `AuthorizationOptions` proporcionado en una `IServiceCollection.AddAuthorization` llamada.

Personalice este comportamiento registrando una implementación diferente `IAuthorizationPolicyProvider` en el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) de la aplicación. 

La `IAuthorizationPolicyProvider` interfaz contiene tres API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) devuelve una directiva de autorización para un nombre determinado.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) devuelve la Directiva de autorización predeterminada (la Directiva que se usa para `[Authorize]` los atributos sin una directiva especificada). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) devuelve la Directiva de autorización de reserva (la Directiva que usa el middleware de autorización cuando no se especifica ninguna directiva). 

Mediante la implementación de estas API, puede personalizar cómo se proporcionan las directivas de autorización.

## <a name="parameterized-authorize-attribute-example"></a>Ejemplo de atributo Authorize parametrizado

Un escenario en `IAuthorizationPolicyProvider` el que resulta útil es habilitar `[Authorize]` atributos personalizados cuyos requisitos dependen de un parámetro. Por ejemplo, en la documentación de [autorización basada en directivas](xref:security/authorization/policies) , se usó una directiva basada en la edad ("AtLeast21") como ejemplo. Si hay diferentes acciones de controlador en una aplicación que deben estar disponibles para los usuarios de *diferentes* edades, podría ser útil tener muchas directivas diferentes basadas en la edad. En lugar de registrar todas las directivas basadas en la edad diferentes que necesitará la aplicación en `AuthorizationOptions` , puede generar las directivas dinámicamente con un personalizado `IAuthorizationPolicyProvider` . Para facilitar el uso de las directivas, puede anotar acciones con el atributo de autorización personalizado como `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>Atributos de autorización personalizados

Las directivas de autorización se identifican por sus nombres. El personalizado `MinimumAgeAuthorizeAttribute` descrito previamente necesita asignar argumentos en una cadena que se puede usar para recuperar la Directiva de autorización correspondiente. Para ello, puede derivar de `AuthorizeAttribute` y hacer que la `Age` propiedad ajuste la `AuthorizeAttribute.Policy` propiedad.

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

Este tipo de atributo tiene una `Policy` cadena basada en el prefijo codificado de forma rígida ( `"MinimumAge"` ) y un entero que se pasa a través del constructor.

Puede aplicarlo a acciones de la misma manera que a otros `Authorize` atributos, salvo que toma un entero como parámetro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personalizado

El personalizado `MinimumAgeAuthorizeAttribute` facilita la solicitud de directivas de autorización para cualquier edad mínima deseada. El siguiente problema es asegurarse de que las directivas de autorización están disponibles para todas esas edades. Aquí es donde `IAuthorizationPolicyProvider` es útil.

Al usar `MinimumAgeAuthorizationAttribute` , los nombres de las directivas de autorización seguirán el patrón `"MinimumAge" + Age` , por lo que el personalizado `IAuthorizationPolicyProvider` debe generar directivas de autorización mediante:

* Analizando la edad del nombre de la Directiva.
* Usar `AuthorizationPolicyBuilder` para crear un nuevo`AuthorizationPolicy`
* En este y en los ejemplos siguientes se asumirá que el usuario se autentica a través de un cookie . `AuthorizationPolicyBuilder`Se debe construir con al menos un nombre de esquema de autorización o siempre se realiza correctamente. De lo contrario, no hay información sobre cómo proporcionar un desafío al usuario y se producirá una excepción.
* Agregar requisitos a la Directiva en función de la edad con `AuthorizationPolicyBuilder.AddRequirements` . En otros escenarios, puede usar `RequireClaim` , `RequireRole` o `RequireUserName` en su lugar.

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Varios proveedores de directivas de autorización

Al usar `IAuthorizationPolicyProvider` implementaciones personalizadas, tenga en cuenta que ASP.net Core solo utiliza una instancia de `IAuthorizationPolicyProvider` . Si un proveedor personalizado no puede proporcionar directivas de autorización para todos los nombres de directiva que se van a usar, debe diferir a un proveedor de copia de seguridad. 

Por ejemplo, considere una aplicación que necesita directivas de edad personalizadas y una recuperación de directiva basada en roles más tradicional. Este tipo de aplicación podría usar un proveedor de directivas de autorización personalizado que:

* Intenta analizar los nombres de directiva. 
* Llama a un proveedor de directivas diferente (como `DefaultAuthorizationPolicyProvider` ) si el nombre de la Directiva no contiene una edad.

La implementación de ejemplo `IAuthorizationPolicyProvider` mostrada anteriormente se puede actualizar para usar `DefaultAuthorizationPolicyProvider` mediante la creación de un proveedor de directivas de copia de seguridad en su constructor (que se usará en caso de que el nombre de la Directiva no coincida con el patrón esperado de ' minimal ' + Age).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

A continuación, el `GetPolicyAsync` método se puede actualizar para utilizar en `BackupPolicyProvider` lugar de devolver null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Directiva predeterminada

Además de proporcionar directivas de autorización con nombre, un personalizado `IAuthorizationPolicyProvider` debe implementar `GetDefaultPolicyAsync` para proporcionar una directiva de autorización para `[Authorize]` los atributos sin un nombre de directiva especificado.

En muchos casos, este atributo de autorización solo requiere un usuario autenticado, por lo que puede crear la Directiva necesaria con una llamada a `RequireAuthenticatedUser` :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Como con todos los aspectos de un personalizado `IAuthorizationPolicyProvider` , puede personalizarlo según sea necesario. En algunos casos, puede ser deseable recuperar la directiva predeterminada de una reserva `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>Directiva de reserva

Un personalizado `IAuthorizationPolicyProvider` puede implementar opcionalmente `GetFallbackPolicyAsync` para proporcionar una directiva que se usa al [combinar directivas](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) y cuando no se especifican directivas. Si `GetFallbackPolicyAsync` devuelve una directiva que no es null, el middleware de autorización usa la Directiva devuelta cuando no se especifican directivas para la solicitud.

Si no se requiere ninguna directiva de reserva, el proveedor puede devolver `null` o aplazar el proveedor de reserva:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Usar un IAuthorizationPolicyProvider personalizado

Para usar las directivas personalizadas de un `IAuthorizationPolicyProvider` , ***debe***:

* Registre los `AuthorizationHandler` tipos adecuados con la inserción de dependencias (descrita en [la autorización basada en directivas](xref:security/authorization/policies#authorization-handlers)), al igual que con todos los escenarios de autorización basados en directivas.
* Registre el `IAuthorizationPolicyProvider` tipo personalizado en la colección de servicios de inserción de dependencias de la aplicación en `Startup.ConfigureServices` para reemplazar el proveedor de directivas predeterminado.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

`IAuthorizationPolicyProvider`Hay disponible un ejemplo personalizado completo en el [repositorio de github dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
