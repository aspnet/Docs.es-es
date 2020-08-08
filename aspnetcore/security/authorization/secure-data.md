---
title: Creación de una aplicación ASP.NET Core con datos de usuario protegidos por autorización
author: rick-anderson
description: Obtenga información sobre cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización. Incluye HTTPS, autenticación, seguridad, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: 44777369693f9eb29d78c3ba638db2e692f430ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021197"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="181d2-104">Creación de una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización</span><span class="sxs-lookup"><span data-stu-id="181d2-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="181d2-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="181d2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="181d2-106">Vea [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="181d2-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="181d2-107">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="181d2-108">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="181d2-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="181d2-109">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="181d2-109">There are three security groups:</span></span>

* <span data-ttu-id="181d2-110">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="181d2-111">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="181d2-112">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="181d2-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="181d2-113">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="181d2-114">Las imágenes de este documento no coinciden exactamente con las plantillas más recientes.</span><span class="sxs-lookup"><span data-stu-id="181d2-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="181d2-115">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="181d2-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="181d2-116">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="181d2-117">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="181d2-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="181d2-118">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="181d2-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="181d2-120">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="181d2-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="181d2-122">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="181d2-122">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="181d2-124">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="181d2-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="181d2-125">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="181d2-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="181d2-127">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="181d2-127">The administrator has all privileges.</span></span> <span data-ttu-id="181d2-128">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="181d2-129">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="181d2-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="181d2-130">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="181d2-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="181d2-131">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="181d2-132">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="181d2-133">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="181d2-134">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="181d2-134">Prerequisites</span></span>

<span data-ttu-id="181d2-135">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="181d2-135">This tutorial is advanced.</span></span> <span data-ttu-id="181d2-136">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="181d2-136">You should be familiar with:</span></span>

* [<span data-ttu-id="181d2-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="181d2-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="181d2-138">Autenticación</span><span class="sxs-lookup"><span data-stu-id="181d2-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="181d2-139">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="181d2-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="181d2-140">Autorización</span><span class="sxs-lookup"><span data-stu-id="181d2-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="181d2-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="181d2-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="181d2-142">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="181d2-142">The starter and completed app</span></span>

<span data-ttu-id="181d2-143">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="181d2-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="181d2-144">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="181d2-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="181d2-145">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="181d2-145">The starter app</span></span>

<span data-ttu-id="181d2-146">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="181d2-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="181d2-147">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="181d2-148">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-148">Secure user data</span></span>

<span data-ttu-id="181d2-149">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="181d2-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="181d2-150">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="181d2-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="181d2-151">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-151">Tie the contact data to the user</span></span>

<span data-ttu-id="181d2-152">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="181d2-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="181d2-153">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="181d2-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="181d2-154">`OwnerID`es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="181d2-155">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="181d2-156">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="181d2-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="181d2-157">Agregar servicios de función aIdentity</span><span class="sxs-lookup"><span data-stu-id="181d2-157">Add Role services to Identity</span></span>

<span data-ttu-id="181d2-158">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="181d2-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="181d2-159">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="181d2-159">Require authenticated users</span></span>

<span data-ttu-id="181d2-160">Establezca la Directiva de autenticación de reserva para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="181d2-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="181d2-161">El código resaltado anterior establece la [Directiva de autenticación de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="181d2-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="181d2-162">La Directiva de autenticación de reserva requiere que ***todos*** los usuarios se autentiquen, excepto Razor las páginas, los controladores o los métodos de acción con un atributo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="181d2-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="181d2-163">Por ejemplo, Razor páginas, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autenticación aplicado en lugar de la Directiva de autenticación de reserva.</span><span class="sxs-lookup"><span data-stu-id="181d2-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="181d2-164">La Directiva de autenticación de reserva:</span><span class="sxs-lookup"><span data-stu-id="181d2-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="181d2-165">Se aplica a todas las solicitudes que no especifican explícitamente una directiva de autenticación.</span><span class="sxs-lookup"><span data-stu-id="181d2-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="181d2-166">En el caso de las solicitudes atendidas por el enrutamiento del punto de conexión, esto incluiría cualquier punto de conexión que no especifique un atributo de autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="181d2-167">En el caso de las solicitudes atendidas por otro middleware después del middleware de autorización, como [archivos estáticos](xref:fundamentals/static-files), esto aplicaría la Directiva a todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="181d2-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="181d2-168">La configuración de la Directiva de autenticación de reserva para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="181d2-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="181d2-169">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="181d2-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="181d2-170">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> clase también contiene <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="181d2-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="181d2-171">`DefaultPolicy`Es la Directiva que se usa con el `[Authorize]` atributo cuando no se especifica ninguna directiva.</span><span class="sxs-lookup"><span data-stu-id="181d2-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="181d2-172">`[Authorize]`no contiene una directiva con nombre, a diferencia de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="181d2-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="181d2-173">Para obtener más información acerca de las directivas, vea <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="181d2-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="181d2-174">Una forma alternativa para que las páginas y los controladores de MVC Razor requieran que todos los usuarios se autentiquen es agregar un filtro de autorización:</span><span class="sxs-lookup"><span data-stu-id="181d2-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="181d2-175">El código anterior usa un filtro de autorización y el establecimiento de la Directiva de reserva usa el enrutamiento del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="181d2-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="181d2-176">La configuración de la Directiva de reserva es la manera preferida de requerir la autenticación de todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="181d2-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="181d2-177">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las `Index` `Privacy` páginas y para que los usuarios anónimos puedan obtener información sobre el sitio antes de que se registren:</span><span class="sxs-lookup"><span data-stu-id="181d2-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="181d2-178">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="181d2-178">Configure the test account</span></span>

<span data-ttu-id="181d2-179">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="181d2-180">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="181d2-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="181d2-181">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="181d2-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="181d2-182">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="181d2-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="181d2-183">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="181d2-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="181d2-184">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="181d2-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="181d2-185">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="181d2-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="181d2-186">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="181d2-187">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="181d2-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="181d2-188">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="181d2-189">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="181d2-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="181d2-190">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="181d2-191">Cree una `ContactIsOwnerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="181d2-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="181d2-192">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="181d2-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="181d2-193">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="181d2-194">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="181d2-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="181d2-195">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="181d2-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="181d2-196">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="181d2-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="181d2-197">`Task.CompletedTask`no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="181d2-198">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="181d2-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="181d2-199">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="181d2-200">`ContactIsOwnerAuthorizationHandler`no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="181d2-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="181d2-201">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-201">Create a manager authorization handler</span></span>

<span data-ttu-id="181d2-202">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="181d2-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="181d2-203">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="181d2-204">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="181d2-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="181d2-205">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="181d2-206">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="181d2-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="181d2-207">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="181d2-208">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="181d2-209">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="181d2-209">Register the authorization handlers</span></span>

<span data-ttu-id="181d2-210">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="181d2-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="181d2-211">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="181d2-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="181d2-212">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="181d2-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="181d2-213">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="181d2-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="181d2-214">`ContactAdministratorsAuthorizationHandler`y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="181d2-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="181d2-215">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="181d2-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="181d2-216">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="181d2-216">Support authorization</span></span>

<span data-ttu-id="181d2-217">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="181d2-218">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="181d2-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="181d2-219">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="181d2-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="181d2-220">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="181d2-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="181d2-221">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="181d2-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="181d2-222">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="181d2-223">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="181d2-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="181d2-224">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="181d2-224">The preceding code:</span></span>

* <span data-ttu-id="181d2-225">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="181d2-226">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="181d2-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="181d2-227">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="181d2-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="181d2-228">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="181d2-228">Update the CreateModel</span></span>

<span data-ttu-id="181d2-229">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="181d2-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="181d2-230">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="181d2-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="181d2-231">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="181d2-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="181d2-232">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="181d2-233">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="181d2-233">Update the IndexModel</span></span>

<span data-ttu-id="181d2-234">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="181d2-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="181d2-235">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="181d2-235">Update the EditModel</span></span>

<span data-ttu-id="181d2-236">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="181d2-237">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="181d2-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="181d2-238">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="181d2-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="181d2-239">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="181d2-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="181d2-240">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="181d2-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="181d2-241">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="181d2-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="181d2-242">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="181d2-242">Update the DeleteModel</span></span>

<span data-ttu-id="181d2-243">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="181d2-244">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="181d2-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="181d2-245">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="181d2-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="181d2-246">Inserte el servicio de autorización en el archivo *pages/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="181d2-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="181d2-247">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="181d2-248">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="181d2-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="181d2-249">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="181d2-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="181d2-250">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="181d2-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="181d2-251">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="181d2-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="181d2-252">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="181d2-253">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="181d2-253">Update Details</span></span>

<span data-ttu-id="181d2-254">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="181d2-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="181d2-255">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="181d2-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="181d2-256">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="181d2-256">Add or remove a user to a role</span></span>

<span data-ttu-id="181d2-257">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="181d2-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="181d2-258">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="181d2-258">Removing privileges from a user.</span></span> <span data-ttu-id="181d2-259">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="181d2-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="181d2-260">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="181d2-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="181d2-261">Diferencias entre desafío y prohibido</span><span class="sxs-lookup"><span data-stu-id="181d2-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="181d2-262">Esta aplicación establece la directiva predeterminada para [requerir usuarios autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="181d2-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="181d2-263">El código siguiente permite usuarios anónimos.</span><span class="sxs-lookup"><span data-stu-id="181d2-263">The following code allows anonymous users.</span></span> <span data-ttu-id="181d2-264">Los usuarios anónimos pueden mostrar las diferencias entre desafío y prohibido.</span><span class="sxs-lookup"><span data-stu-id="181d2-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="181d2-265">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="181d2-265">In the preceding code:</span></span>

* <span data-ttu-id="181d2-266">Cuando el usuario **no** está autenticado, `ChallengeResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="181d2-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="181d2-267">Cuando `ChallengeResult` se devuelve, se redirige al usuario a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="181d2-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="181d2-268">Cuando el usuario está autenticado, pero no autorizado, `ForbidResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="181d2-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="181d2-269">Cuando `ForbidResult` se devuelve, se redirige al usuario a la página acceso denegado.</span><span class="sxs-lookup"><span data-stu-id="181d2-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="181d2-270">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="181d2-270">Test the completed app</span></span>

<span data-ttu-id="181d2-271">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="181d2-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="181d2-272">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="181d2-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="181d2-273">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="181d2-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="181d2-274">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="181d2-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="181d2-275">Si la aplicación tiene contactos:</span><span class="sxs-lookup"><span data-stu-id="181d2-275">If the app has contacts:</span></span>

* <span data-ttu-id="181d2-276">Elimine todos los registros de la `Contact` tabla.</span><span class="sxs-lookup"><span data-stu-id="181d2-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="181d2-277">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="181d2-278">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="181d2-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="181d2-279">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="181d2-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="181d2-280">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="181d2-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="181d2-281">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="181d2-281">Verify the following operations:</span></span>

* <span data-ttu-id="181d2-282">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="181d2-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="181d2-283">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="181d2-284">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="181d2-285">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="181d2-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="181d2-286">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="181d2-287">Usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-287">User</span></span>                | <span data-ttu-id="181d2-288">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="181d2-288">Seeded by the app</span></span> | <span data-ttu-id="181d2-289">Opciones</span><span class="sxs-lookup"><span data-stu-id="181d2-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="181d2-290">No</span><span class="sxs-lookup"><span data-stu-id="181d2-290">No</span></span>                | <span data-ttu-id="181d2-291">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="181d2-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="181d2-292">Sí</span><span class="sxs-lookup"><span data-stu-id="181d2-292">Yes</span></span>               | <span data-ttu-id="181d2-293">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="181d2-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="181d2-294">Sí</span><span class="sxs-lookup"><span data-stu-id="181d2-294">Yes</span></span>               | <span data-ttu-id="181d2-295">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="181d2-296">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="181d2-297">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="181d2-298">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="181d2-299">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="181d2-299">Create the starter app</span></span>

* <span data-ttu-id="181d2-300">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="181d2-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="181d2-301">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="181d2-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="181d2-302">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="181d2-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="181d2-303">`-uld`especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="181d2-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="181d2-304">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="181d2-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="181d2-305">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="181d2-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="181d2-306">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="181d2-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="181d2-307">Si experimenta un error con el `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema de github](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="181d2-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="181d2-308">Actualice el delimitador **ContactManager** en el archivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="181d2-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="181d2-309">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="181d2-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="181d2-310">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="181d2-310">Seed the database</span></span>

<span data-ttu-id="181d2-311">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) a la carpeta de *datos* :</span><span class="sxs-lookup"><span data-stu-id="181d2-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="181d2-312">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="181d2-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="181d2-313">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-313">Test that the app seeded the database.</span></span> <span data-ttu-id="181d2-314">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="181d2-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="181d2-315">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="181d2-316">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="181d2-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="181d2-317">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="181d2-317">There are three security groups:</span></span>

* <span data-ttu-id="181d2-318">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="181d2-319">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="181d2-320">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="181d2-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="181d2-321">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="181d2-322">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="181d2-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="181d2-323">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="181d2-324">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="181d2-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="181d2-325">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="181d2-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="181d2-327">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="181d2-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="181d2-329">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="181d2-329">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="181d2-331">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="181d2-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="181d2-332">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="181d2-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="181d2-334">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="181d2-334">The administrator has all privileges.</span></span> <span data-ttu-id="181d2-335">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="181d2-336">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="181d2-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="181d2-337">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="181d2-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="181d2-338">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="181d2-339">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="181d2-340">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="181d2-341">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="181d2-341">Prerequisites</span></span>

<span data-ttu-id="181d2-342">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="181d2-342">This tutorial is advanced.</span></span> <span data-ttu-id="181d2-343">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="181d2-343">You should be familiar with:</span></span>

* [<span data-ttu-id="181d2-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="181d2-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="181d2-345">Autenticación</span><span class="sxs-lookup"><span data-stu-id="181d2-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="181d2-346">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="181d2-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="181d2-347">Autorización</span><span class="sxs-lookup"><span data-stu-id="181d2-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="181d2-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="181d2-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="181d2-349">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="181d2-349">The starter and completed app</span></span>

<span data-ttu-id="181d2-350">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="181d2-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="181d2-351">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="181d2-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="181d2-352">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="181d2-352">The starter app</span></span>

<span data-ttu-id="181d2-353">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="181d2-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="181d2-354">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="181d2-355">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-355">Secure user data</span></span>

<span data-ttu-id="181d2-356">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="181d2-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="181d2-357">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="181d2-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="181d2-358">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-358">Tie the contact data to the user</span></span>

<span data-ttu-id="181d2-359">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="181d2-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="181d2-360">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="181d2-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="181d2-361">`OwnerID`es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="181d2-362">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="181d2-363">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="181d2-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="181d2-364">Agregar servicios de función aIdentity</span><span class="sxs-lookup"><span data-stu-id="181d2-364">Add Role services to Identity</span></span>

<span data-ttu-id="181d2-365">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="181d2-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="181d2-366">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="181d2-366">Require authenticated users</span></span>

<span data-ttu-id="181d2-367">Establezca la Directiva de autenticación predeterminada para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="181d2-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="181d2-368">Puede rechazar la autenticación en el nivel de la Razor página, el controlador o el método de acción con el `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="181d2-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="181d2-369">La configuración de la Directiva de autenticación predeterminada para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="181d2-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="181d2-370">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="181d2-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="181d2-371">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las páginas index, about y Contact para que los usuarios anónimos puedan obtener información sobre el sitio antes de registrarse.</span><span class="sxs-lookup"><span data-stu-id="181d2-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="181d2-372">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="181d2-372">Configure the test account</span></span>

<span data-ttu-id="181d2-373">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="181d2-374">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="181d2-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="181d2-375">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="181d2-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="181d2-376">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="181d2-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="181d2-377">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="181d2-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="181d2-378">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="181d2-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="181d2-379">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="181d2-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="181d2-380">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="181d2-381">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="181d2-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="181d2-382">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="181d2-383">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="181d2-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="181d2-384">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="181d2-385">Cree una carpeta de *autorización* y cree una `ContactIsOwnerAuthorizationHandler` clase en ella.</span><span class="sxs-lookup"><span data-stu-id="181d2-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="181d2-386">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="181d2-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="181d2-387">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="181d2-388">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="181d2-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="181d2-389">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="181d2-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="181d2-390">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="181d2-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="181d2-391">`Task.CompletedTask`no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="181d2-392">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="181d2-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="181d2-393">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="181d2-394">`ContactIsOwnerAuthorizationHandler`no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="181d2-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="181d2-395">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-395">Create a manager authorization handler</span></span>

<span data-ttu-id="181d2-396">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="181d2-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="181d2-397">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="181d2-398">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="181d2-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="181d2-399">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="181d2-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="181d2-400">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="181d2-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="181d2-401">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="181d2-402">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="181d2-403">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="181d2-403">Register the authorization handlers</span></span>

<span data-ttu-id="181d2-404">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="181d2-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="181d2-405">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="181d2-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="181d2-406">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="181d2-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="181d2-407">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="181d2-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="181d2-408">`ContactAdministratorsAuthorizationHandler`y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="181d2-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="181d2-409">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="181d2-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="181d2-410">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="181d2-410">Support authorization</span></span>

<span data-ttu-id="181d2-411">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="181d2-412">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="181d2-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="181d2-413">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="181d2-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="181d2-414">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="181d2-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="181d2-415">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="181d2-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="181d2-416">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="181d2-417">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="181d2-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="181d2-418">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="181d2-418">The preceding code:</span></span>

* <span data-ttu-id="181d2-419">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="181d2-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="181d2-420">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="181d2-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="181d2-421">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="181d2-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="181d2-422">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="181d2-422">Update the CreateModel</span></span>

<span data-ttu-id="181d2-423">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="181d2-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="181d2-424">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="181d2-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="181d2-425">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="181d2-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="181d2-426">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="181d2-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="181d2-427">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="181d2-427">Update the IndexModel</span></span>

<span data-ttu-id="181d2-428">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="181d2-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="181d2-429">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="181d2-429">Update the EditModel</span></span>

<span data-ttu-id="181d2-430">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="181d2-431">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="181d2-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="181d2-432">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="181d2-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="181d2-433">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="181d2-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="181d2-434">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="181d2-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="181d2-435">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="181d2-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="181d2-436">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="181d2-436">Update the DeleteModel</span></span>

<span data-ttu-id="181d2-437">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="181d2-438">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="181d2-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="181d2-439">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="181d2-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="181d2-440">Inserte el servicio de autorización en el archivo *views/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="181d2-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="181d2-441">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="181d2-442">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="181d2-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="181d2-443">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="181d2-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="181d2-444">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="181d2-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="181d2-445">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="181d2-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="181d2-446">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="181d2-447">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="181d2-447">Update Details</span></span>

<span data-ttu-id="181d2-448">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="181d2-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="181d2-449">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="181d2-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="181d2-450">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="181d2-450">Add or remove a user to a role</span></span>

<span data-ttu-id="181d2-451">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="181d2-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="181d2-452">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="181d2-452">Removing privileges from a user.</span></span> <span data-ttu-id="181d2-453">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="181d2-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="181d2-454">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="181d2-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="181d2-455">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="181d2-455">Test the completed app</span></span>

<span data-ttu-id="181d2-456">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="181d2-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="181d2-457">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="181d2-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="181d2-458">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="181d2-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="181d2-459">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="181d2-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="181d2-460">Quitar y actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="181d2-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="181d2-461">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="181d2-462">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="181d2-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="181d2-463">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="181d2-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="181d2-464">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="181d2-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="181d2-465">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="181d2-465">Verify the following operations:</span></span>

* <span data-ttu-id="181d2-466">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="181d2-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="181d2-467">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="181d2-468">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="181d2-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="181d2-469">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="181d2-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="181d2-470">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="181d2-471">Usuario</span><span class="sxs-lookup"><span data-stu-id="181d2-471">User</span></span>                | <span data-ttu-id="181d2-472">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="181d2-472">Seeded by the app</span></span> | <span data-ttu-id="181d2-473">Opciones</span><span class="sxs-lookup"><span data-stu-id="181d2-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="181d2-474">No</span><span class="sxs-lookup"><span data-stu-id="181d2-474">No</span></span>                | <span data-ttu-id="181d2-475">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="181d2-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="181d2-476">Sí</span><span class="sxs-lookup"><span data-stu-id="181d2-476">Yes</span></span>               | <span data-ttu-id="181d2-477">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="181d2-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="181d2-478">Sí</span><span class="sxs-lookup"><span data-stu-id="181d2-478">Yes</span></span>               | <span data-ttu-id="181d2-479">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="181d2-480">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="181d2-481">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="181d2-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="181d2-482">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="181d2-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="181d2-483">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="181d2-483">Create the starter app</span></span>

* <span data-ttu-id="181d2-484">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="181d2-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="181d2-485">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="181d2-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="181d2-486">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="181d2-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="181d2-487">`-uld`especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="181d2-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="181d2-488">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="181d2-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="181d2-489">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="181d2-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="181d2-490">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="181d2-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="181d2-491">Actualice el delimitador **ContactManager** en el archivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="181d2-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="181d2-492">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="181d2-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="181d2-493">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="181d2-493">Seed the database</span></span>

<span data-ttu-id="181d2-494">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) a la carpeta de *datos* .</span><span class="sxs-lookup"><span data-stu-id="181d2-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="181d2-495">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="181d2-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="181d2-496">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="181d2-496">Test that the app seeded the database.</span></span> <span data-ttu-id="181d2-497">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="181d2-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="181d2-498">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="181d2-498">Additional resources</span></span>

* [<span data-ttu-id="181d2-499">Creación de una aplicación .NET Core y SQL Database en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="181d2-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="181d2-500">[ASP.net Core laboratorio de autorización](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="181d2-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="181d2-501">En este laboratorio se incluyen más detalles sobre las características de seguridad que se presentan en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="181d2-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="181d2-502">Autorización personalizada basada en directivas</span><span class="sxs-lookup"><span data-stu-id="181d2-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
