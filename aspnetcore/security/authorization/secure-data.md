---
title: Creación de una aplicación ASP.NET Core con datos de usuario protegidos por autorización
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización. Incluye HTTPS, autenticación, seguridad, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854657"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Creación de una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización

Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

Vea [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización. Muestra una lista de contactos que los usuarios autenticados (registrados) han creado. Hay tres grupos de seguridad:

* **Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.
* Los **administradores** pueden aprobar o rechazar datos de contacto. Los usuarios solo pueden ver los contactos aprobados.
* **Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.

Las imágenes de este documento no coinciden exactamente con las plantillas más recientes.

En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión. Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos. Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** . Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

La siguiente imagen muestra la vista de detalles de los administradores de un contacto:

![Vista del administrador de un contacto](secure-data/_static/manager.png)

Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.

En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

El administrador tiene todos los privilegios. Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.

La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

El ejemplo contiene los siguientes controladores de autorización:

* `ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.
* `ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.
* `ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.

## <a name="prerequisites"></a>Requisitos previos

Este tutorial es avanzado. Debe estar familiarizado con:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticación](xref:security/authentication/identity)
* [Confirmación de la cuenta y recuperación de la contraseña](xref:security/authentication/accconfirm)
* [Autorización](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>La aplicación de inicio y completada

[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.

### <a name="the-starter-app"></a>La aplicación de inicio

[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto. Para crear la aplicación de inicio, consulte [crear la aplicación de inicio](#create-the-starter-app).

## <a name="secure-user-data"></a>Proteger los datos de usuario

En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura. Puede que le resulte útil consultar el proyecto completado.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular los datos de contacto al usuario

Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios. Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos. El `Status` campo determina si los usuarios generales pueden ver un contacto.

Cree una nueva migración y actualice la base de datos:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Agregar servicios de función a Identity

Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Requerir usuarios autenticados

Establezca la Directiva de autenticación de reserva para requerir la autenticación de los usuarios:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

El código resaltado anterior establece la [Directiva de autenticación de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). La Directiva de autenticación de reserva requiere **_All_* _ usuarios para autenticarse, excepto Razor las páginas, los controladores o los métodos de acción con un atributo de autenticación. Por ejemplo, Razor páginas, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autenticación aplicado en lugar de la Directiva de autenticación de reserva.

La Directiva de autenticación de reserva:

_ Se aplica a todas las solicitudes que no especifican explícitamente una directiva de autenticación. En el caso de las solicitudes atendidas por el enrutamiento del punto de conexión, esto incluiría cualquier punto de conexión que no especifique un atributo de autorización. En el caso de las solicitudes atendidas por otro middleware después del middleware de autorización, como [archivos estáticos](xref:fundamentals/static-files), esto aplicaría la Directiva a todas las solicitudes.

La configuración de la Directiva de autenticación de reserva para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados. Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> clase también contiene <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . `DefaultPolicy`Es la Directiva que se usa con el `[Authorize]` atributo cuando no se especifica ninguna directiva. `[Authorize]` no contiene una directiva con nombre, a diferencia de `[Authorize(PolicyName="MyPolicy")]` .

Para obtener más información acerca de las directivas, vea <xref:security/authorization/policies> .

Una forma alternativa para que las páginas y los controladores de MVC Razor requieran que todos los usuarios se autentiquen es agregar un filtro de autorización:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

El código anterior usa un filtro de autorización y el establecimiento de la Directiva de reserva usa el enrutamiento del punto de conexión. La configuración de la Directiva de reserva es la manera preferida de requerir la autenticación de todos los usuarios.

Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las `Index` `Privacy` páginas y para que los usuarios anónimos puedan obtener información sobre el sitio antes de que se registren:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Configurar la cuenta de prueba

La `SeedData` clase crea dos cuentas: administrador y administrador. Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas. Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.

Actualice `Main` para usar la contraseña de prueba:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Crear las cuentas de prueba y actualizar los contactos

Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Agregue el identificador de usuario de administrador y `ContactStatus` los contactos. Haga que uno de los contactos "enviado" y otro "rechazado". Agregue el identificador de usuario y el estado a todos los contactos. Solo se muestra un contacto:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Crear controladores de autorización de propietario, administrador y administrador

Cree una `ContactIsOwnerAuthorizationHandler` clase en la carpeta de *autorización* . `ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto. Los controladores de autorización generalmente:

* Devuelve `context.Succeed` cuando se cumplen los requisitos.
* Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos. `Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.

Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos. `ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Crear un controlador de autorización de administrador

Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* . `ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador. Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Crear un controlador de autorización de administrador

Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* . `ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador. El administrador puede realizar todas las operaciones.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar los controladores de autorización

Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core. Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection). Agregue el código siguiente al final de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons. Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.

## <a name="support-authorization"></a>Autorización de soporte técnico

En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.

### <a name="review-the-contact-operations-requirements-class"></a>Revisar la clase de requisitos de las operaciones de contacto

Revise la `ContactOperations` clase. Esta clase contiene los requisitos que admite la aplicación:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Crear una clase base para las páginas de contactos Razor

Cree una clase base que contenga los servicios usados en las Razor páginas de contactos. La clase base coloca el código de inicialización en una ubicación:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

El código anterior:

* Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.
* Agrega el Identity `UserManager` servicio.
* Agregue la `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Actualización de CreateModel

Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Actualice el `CreateModel.OnPostAsync` método a:

* Agregue el identificador de usuario al `Contact` modelo.
* Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Actualización de IndexModel

Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Actualización de EditModel

Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto. Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente. La aplicación no tiene acceso al recurso cuando se evalúan los atributos. La autorización basada en recursos debe ser imperativa. Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador. Frecuentemente tiene acceso al recurso pasando la clave de recurso.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Actualización de DeleteModel

Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Inyectar el servicio de autorización en las vistas

Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.

Inserte el servicio de autorización en el archivo *pages/_ViewImports. cshtml* para que esté disponible para todas las vistas:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

El marcado anterior agrega varias `using` instrucciones.

Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación. La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos. Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad. La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.

### <a name="update-details"></a>Detalles de la actualización

Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Actualice el modelo de página de detalles:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Agregar o quitar un usuario de un rol

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:

* Quitar privilegios de un usuario. Por ejemplo, silenciar a un usuario en una aplicación de chat.
* Agregar privilegios a un usuario.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Diferencias entre desafío y prohibido

Esta aplicación establece la directiva predeterminada para [requerir usuarios autenticados](#rau). El código siguiente permite usuarios anónimos. Los usuarios anónimos pueden mostrar las diferencias entre desafío y prohibido.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

En el código anterior:

* Cuando el usuario **no** está autenticado, `ChallengeResult` se devuelve un. Cuando `ChallengeResult` se devuelve, se redirige al usuario a la página de inicio de sesión.
* Cuando el usuario está autenticado, pero no autorizado, `ForbidResult` se devuelve un. Cuando `ForbidResult` se devuelve, se redirige al usuario a la página acceso denegado.

## <a name="test-the-completed-app"></a>Prueba de la aplicación completada

Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:

* Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo. Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.
* Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Si la aplicación tiene contactos:

* Elimine todos los registros de la `Contact` tabla.
* Reinicie la aplicación para inicializar la base de datos.

Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate). En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ). Inicie sesión en cada explorador con un usuario diferente. Compruebe las siguientes operaciones:

* Los usuarios registrados pueden ver todos los datos de contacto aprobados.
* Los usuarios registrados pueden editar o eliminar sus propios datos.
* Los administradores pueden aprobar o rechazar datos de contacto. La `Details` vista muestra los botones **aprobar** y **rechazar** .
* Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.

| Usuario                | Inicializado por la aplicación | Opciones                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Edite o elimine los datos propios.                |
| manager@contoso.com | Sí               | Aprobar/rechazar y editar o eliminar los datos propios. |
| admin@contoso.com   | Sí               | Aprobar/rechazar y editar o eliminar todos los datos. |

Cree un contacto en el explorador del administrador. Copie la dirección URL para eliminar y editar del contacto del administrador. Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.

## <a name="create-the-starter-app"></a>Creación de la aplicación de inicio

* Crear una Razor aplicación de páginas denominada "ContactManager"
  * Cree la aplicación con **cuentas de usuario individuales**.
  * Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.
  * `-uld` especifica LocalDB en lugar de SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Agregue *Models/contact. CS*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Aplicar scaffolding al `Contact` modelo.
* Cree la migración inicial y actualice la base de datos:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Si experimenta un error con el `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema de github](https://github.com/aspnet/Scaffolding/issues/984).

* Actualice el delimitador **ContactManager** en el archivo *pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Prueba de la aplicación mediante la creación, edición y eliminación de un contacto

### <a name="seed-the-database"></a>Inicializar la base de datos

Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) a la carpeta de *datos* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Llamada `SeedData.Initialize` desde `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Compruebe que la aplicación ha inicializado la base de datos. Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización. Muestra una lista de contactos que los usuarios autenticados (registrados) han creado. Hay tres grupos de seguridad:

* **Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.
* Los **administradores** pueden aprobar o rechazar datos de contacto. Los usuarios solo pueden ver los contactos aprobados.
* **Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.

En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión. Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos. Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** . Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

La siguiente imagen muestra la vista de detalles de los administradores de un contacto:

![Vista del administrador de un contacto](secure-data/_static/manager.png)

Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.

En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

El administrador tiene todos los privilegios. Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.

La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

El ejemplo contiene los siguientes controladores de autorización:

* `ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.
* `ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.
* `ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.

## <a name="prerequisites"></a>Requisitos previos

Este tutorial es avanzado. Debe estar familiarizado con:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticación](xref:security/authentication/identity)
* [Confirmación de la cuenta y recuperación de la contraseña](xref:security/authentication/accconfirm)
* [Autorización](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>La aplicación de inicio y completada

[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.

### <a name="the-starter-app"></a>La aplicación de inicio

[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.

## <a name="secure-user-data"></a>Proteger los datos de usuario

En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura. Puede que le resulte útil consultar el proyecto completado.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular los datos de contacto al usuario

Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios. Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos. El `Status` campo determina si los usuarios generales pueden ver un contacto.

Cree una nueva migración y actualice la base de datos:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Agregar servicios de función a Identity

Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Requerir usuarios autenticados

Establezca la Directiva de autenticación predeterminada para requerir la autenticación de los usuarios:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Puede rechazar la autenticación en el nivel de la Razor página, el controlador o el método de acción con el `[AllowAnonymous]` atributo. La configuración de la Directiva de autenticación predeterminada para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados. Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.

Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las páginas index, about y Contact para que los usuarios anónimos puedan obtener información sobre el sitio antes de registrarse.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Configurar la cuenta de prueba

La `SeedData` clase crea dos cuentas: administrador y administrador. Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas. Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.

Actualice `Main` para usar la contraseña de prueba:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Crear las cuentas de prueba y actualizar los contactos

Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Agregue el identificador de usuario de administrador y `ContactStatus` los contactos. Haga que uno de los contactos "enviado" y otro "rechazado". Agregue el identificador de usuario y el estado a todos los contactos. Solo se muestra un contacto:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Crear controladores de autorización de propietario, administrador y administrador

Cree una carpeta de *autorización* y cree una `ContactIsOwnerAuthorizationHandler` clase en ella. `ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto. Los controladores de autorización generalmente:

* Devuelve `context.Succeed` cuando se cumplen los requisitos.
* Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos. `Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.

Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos. `ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Crear un controlador de autorización de administrador

Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* . `ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador. Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Crear un controlador de autorización de administrador

Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* . `ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador. El administrador puede realizar todas las operaciones.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar los controladores de autorización

Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core. Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection). Agregue el código siguiente al final de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons. Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.

## <a name="support-authorization"></a>Autorización de soporte técnico

En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.

### <a name="review-the-contact-operations-requirements-class"></a>Revisar la clase de requisitos de las operaciones de contacto

Revise la `ContactOperations` clase. Esta clase contiene los requisitos que admite la aplicación:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Crear una clase base para las páginas de contactos Razor

Cree una clase base que contenga los servicios usados en las Razor páginas de contactos. La clase base coloca el código de inicialización en una ubicación:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

El código anterior:

* Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.
* Agrega el Identity `UserManager` servicio.
* Agregue la `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Actualización de CreateModel

Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Actualice el `CreateModel.OnPostAsync` método a:

* Agregue el identificador de usuario al `Contact` modelo.
* Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Actualización de IndexModel

Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Actualización de EditModel

Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto. Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente. La aplicación no tiene acceso al recurso cuando se evalúan los atributos. La autorización basada en recursos debe ser imperativa. Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador. Frecuentemente tiene acceso al recurso pasando la clave de recurso.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Actualización de DeleteModel

Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Inyectar el servicio de autorización en las vistas

Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.

Inserte el servicio de autorización en el archivo *views/_ViewImports. cshtml* para que esté disponible para todas las vistas:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

El marcado anterior agrega varias `using` instrucciones.

Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación. La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos. Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad. La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.

### <a name="update-details"></a>Detalles de la actualización

Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Actualice el modelo de página de detalles:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Agregar o quitar un usuario de un rol

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:

* Quitar privilegios de un usuario. Por ejemplo, silenciar a un usuario en una aplicación de chat.
* Agregar privilegios a un usuario.

## <a name="test-the-completed-app"></a>Prueba de la aplicación completada

Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:

* Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo. Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.
* Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Quitar y actualizar la base de datos

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Reinicie la aplicación para inicializar la base de datos.

Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate). En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ). Inicie sesión en cada explorador con un usuario diferente. Compruebe las siguientes operaciones:

* Los usuarios registrados pueden ver todos los datos de contacto aprobados.
* Los usuarios registrados pueden editar o eliminar sus propios datos.
* Los administradores pueden aprobar o rechazar datos de contacto. La `Details` vista muestra los botones **aprobar** y **rechazar** .
* Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.

| Usuario                | Inicializado por la aplicación | Opciones                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Edite o elimine los datos propios.                |
| manager@contoso.com | Sí               | Aprobar/rechazar y editar o eliminar los datos propios. |
| admin@contoso.com   | Sí               | Aprobar/rechazar y editar o eliminar todos los datos. |

Cree un contacto en el explorador del administrador. Copie la dirección URL para eliminar y editar del contacto del administrador. Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.

## <a name="create-the-starter-app"></a>Creación de la aplicación de inicio

* Crear una Razor aplicación de páginas denominada "ContactManager"
  * Cree la aplicación con **cuentas de usuario individuales**.
  * Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.
  * `-uld` especifica LocalDB en lugar de SQLite.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Agregue *Models/contact. CS*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Aplicar scaffolding al `Contact` modelo.
* Cree la migración inicial y actualice la base de datos:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Actualice el delimitador **ContactManager** en el archivo *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Prueba de la aplicación mediante la creación, edición y eliminación de un contacto

### <a name="seed-the-database"></a>Inicializar la base de datos

Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) a la carpeta de *datos* .

Llamada `SeedData.Initialize` desde `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Compruebe que la aplicación ha inicializado la base de datos. Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Recursos adicionales

* [Creación de una aplicación .NET Core y SQL Database en Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.net Core laboratorio de autorización](https://github.com/blowdart/AspNetAuthorizationWorkshop). En este laboratorio se incluyen más detalles sobre las características de seguridad que se presentan en este tutorial.
* <xref:security/authorization/introduction>
* [Autorización personalizada basada en directivas](xref:security/authorization/policies)
