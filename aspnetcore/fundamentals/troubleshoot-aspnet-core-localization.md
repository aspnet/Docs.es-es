---
title: Solución de problemas de localización de ASP.NET Core
author: hishamco
description: Obtenga información sobre cómo diagnosticar problemas con la localización en aplicaciones de ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053623"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Solución de problemas de localización de ASP.NET Core

Por [Hisham Bin Ateya](https://github.com/hishamco)

En este artículo se proporcionan instrucciones sobre cómo diagnosticar problemas de localización en aplicaciones de ASP.NET Core.

## <a name="localization-configuration-issues"></a>Problemas de configuración de localización

**Orden del middleware de localización**  
Es posible que la aplicación no esté localizada porque el middleware de localización no esté en el orden esperado.

Para resolver este problema, asegúrese de que el middleware de localización esté registrado antes del middleware de MVC. En caso contrario, el middleware de localización no se aplicará.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Ruta de acceso a los recursos de localización no encontrada**

**Error de coincidencia entre las referencias culturales admitidas en RequestCultureProvider y las registradas**  

## <a name="resource-file-naming-issues"></a>Problemas con la nomenclatura de los archivos de recursos

ASP.NET Core ha predefinido reglas y directrices para la nomenclatura de archivos de recursos de localización, que se describen detalladamente [aquí](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

## <a name="missing-resources"></a>Recursos no disponibles

Estas son algunas de las causas que suelen provocar que no se encuentren los archivos:

- Los nombres de los recursos están mal escritos, bien en el archivo `resx`, bien en la solicitud del localizador.
- Falta el recurso en `resx` para algunos idiomas, pero existe para otros.
- Si sigue teniendo problemas, compruebe los mensajes del registro de localización (que están en el nivel de registro `Debug`) para obtener información detallada sobre los recursos que faltan.

_**Sugerencia**: Si usa `CookieRequestCultureProvider`, compruebe que no se usen comillas simples con las referencias culturales dentro del valor de la cookie de localización. Por ejemplo, `c='en-UK'|uic='en-US'` es un valor de cookie no válido, pero `c=en-UK|uic=en-US` es válido._

## <a name="resources--class-libraries-issues"></a>Problemas de las bibliotecas de clases y recursos

De manera predeterminada, ASP.NET Core proporciona un método para permitir que las bibliotecas de clases encuentren los archivos de recursos mediante [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).

Estos son algunos de los problemas habituales con las bibliotecas de clases:
- Falta `ResourceLocationAttribute` en la biblioteca de clases, lo que impide que `ResourceManagerStringLocalizerFactory` detecte los recursos.
- Nomenclatura de los archivos de recursos. Para obtener más información, consulte la sección [Problemas con la nomenclatura de los archivos de recursos](#resource-file-naming-issues).
- Cambio del espacio de nombres raíz de la biblioteca de clases. Para obtener más información, vea la sección [Problemas con el espacio de nombres raíz](#root-namespace-issues).

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider no funciona según lo previsto

La clase `RequestLocalizationOptions` tiene tres proveedores predeterminados:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) permite personalizar el modo en el que se proporciona la referencia cultural de localización en la aplicación. Si los proveedores predeterminados no cumplen con los requisitos, se usa `CustomRequestCultureProvider`.

- Un motivo habitual por el que un proveedor personalizado no funciona adecuadamente es que no es el primero en la lista `RequestCultureProviders`. Para resolver este problema, haga lo siguiente:

- Inserte el proveedor personalizado en la posición 0 de la lista `RequestCultureProviders`, como en el ejemplo siguiente:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Use el método de extensión `AddInitialRequestCultureProvider` para establecer el proveedor personalizado como el inicial.

## <a name="root-namespace-issues"></a>Problemas con el espacio de nombres raíz

Si el espacio de nombres raíz de un ensamblado es distinto al nombre del ensamblado, la localización no funcionará de manera predeterminada. Para evitar este problema, use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), procedimiento que se describe detalladamente [aquí](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).

> [!WARNING]
> Esto puede ocurrir cuando el nombre de un proyecto no es un identificador de .NET válido. Por ejemplo `my-project-name.csproj` usará el espacio de nombres raíz `my_project_name` y el nombre de ensamblado `my-project-name`, lo que lleva a este error. 

## <a name="resources--build-action"></a>Acción de compilación y recursos

Si usa archivos de recursos para la localización, es importante que estos tengan una acción de compilación adecuada. Deben ser **recursos insertados**, puesto que, de lo contrario, `ResourceStringLocalizer` no podrá encontrarlos.
