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
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="ee5c1-104">Creación de una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="ee5c1-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ee5c1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="ee5c1-106">Vea [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="ee5c1-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee5c1-107">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="ee5c1-108">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="ee5c1-109">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-109">There are three security groups:</span></span>

* <span data-ttu-id="ee5c1-110">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="ee5c1-111">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="ee5c1-112">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="ee5c1-113">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="ee5c1-114">Las imágenes de este documento no coinciden exactamente con las plantillas más recientes.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="ee5c1-115">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="ee5c1-116">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="ee5c1-117">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="ee5c1-118">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="ee5c1-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="ee5c1-120">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="ee5c1-122">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-122">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="ee5c1-124">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="ee5c1-125">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="ee5c1-127">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-127">The administrator has all privileges.</span></span> <span data-ttu-id="ee5c1-128">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="ee5c1-129">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="ee5c1-130">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="ee5c1-131">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="ee5c1-132">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="ee5c1-133">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ee5c1-134">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-134">Prerequisites</span></span>

<span data-ttu-id="ee5c1-135">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-135">This tutorial is advanced.</span></span> <span data-ttu-id="ee5c1-136">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-136">You should be familiar with:</span></span>

* [<span data-ttu-id="ee5c1-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee5c1-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="ee5c1-138">Autenticación</span><span class="sxs-lookup"><span data-stu-id="ee5c1-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ee5c1-139">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="ee5c1-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="ee5c1-140">Autorización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="ee5c1-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ee5c1-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="ee5c1-142">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="ee5c1-142">The starter and completed app</span></span>

<span data-ttu-id="ee5c1-143">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="ee5c1-144">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="ee5c1-145">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="ee5c1-145">The starter app</span></span>

<span data-ttu-id="ee5c1-146">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="ee5c1-147">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="ee5c1-148">Para crear la aplicación de inicio, consulte [crear la aplicación de inicio](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="ee5c1-149">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-149">Secure user data</span></span>

<span data-ttu-id="ee5c1-150">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="ee5c1-151">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="ee5c1-152">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-152">Tie the contact data to the user</span></span>

<span data-ttu-id="ee5c1-153">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="ee5c1-154">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="ee5c1-155">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="ee5c1-156">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="ee5c1-157">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="ee5c1-158">Agregar servicios de función a Identity</span><span class="sxs-lookup"><span data-stu-id="ee5c1-158">Add Role services to Identity</span></span>

<span data-ttu-id="ee5c1-159">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="ee5c1-160">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="ee5c1-160">Require authenticated users</span></span>

<span data-ttu-id="ee5c1-161">Establezca la Directiva de autenticación de reserva para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="ee5c1-162">El código resaltado anterior establece la [Directiva de autenticación de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="ee5c1-163">La Directiva de autenticación de reserva requiere \**_All_* _ usuarios para autenticarse, excepto Razor las páginas, los controladores o los métodos de acción con un atributo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="ee5c1-164">Por ejemplo, Razor páginas, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autenticación aplicado en lugar de la Directiva de autenticación de reserva.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="ee5c1-165">La Directiva de autenticación de reserva:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-165">The fallback authentication policy:</span></span>

<span data-ttu-id="ee5c1-166">_ Se aplica a todas las solicitudes que no especifican explícitamente una directiva de autenticación.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="ee5c1-167">En el caso de las solicitudes atendidas por el enrutamiento del punto de conexión, esto incluiría cualquier punto de conexión que no especifique un atributo de autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="ee5c1-168">En el caso de las solicitudes atendidas por otro middleware después del middleware de autorización, como [archivos estáticos](xref:fundamentals/static-files), esto aplicaría la Directiva a todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="ee5c1-169">La configuración de la Directiva de autenticación de reserva para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="ee5c1-170">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="ee5c1-171">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> clase también contiene <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ee5c1-172">`DefaultPolicy`Es la Directiva que se usa con el `[Authorize]` atributo cuando no se especifica ninguna directiva.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="ee5c1-173">`[Authorize]` no contiene una directiva con nombre, a diferencia de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="ee5c1-174">Para obtener más información acerca de las directivas, vea <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="ee5c1-175">Una forma alternativa para que las páginas y los controladores de MVC Razor requieran que todos los usuarios se autentiquen es agregar un filtro de autorización:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="ee5c1-176">El código anterior usa un filtro de autorización y el establecimiento de la Directiva de reserva usa el enrutamiento del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="ee5c1-177">La configuración de la Directiva de reserva es la manera preferida de requerir la autenticación de todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="ee5c1-178">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las `Index` `Privacy` páginas y para que los usuarios anónimos puedan obtener información sobre el sitio antes de que se registren:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="ee5c1-179">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="ee5c1-179">Configure the test account</span></span>

<span data-ttu-id="ee5c1-180">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="ee5c1-181">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="ee5c1-182">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ee5c1-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="ee5c1-183">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="ee5c1-184">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="ee5c1-185">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="ee5c1-186">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="ee5c1-187">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="ee5c1-188">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="ee5c1-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="ee5c1-189">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="ee5c1-190">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="ee5c1-191">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="ee5c1-192">Cree una `ContactIsOwnerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="ee5c1-193">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="ee5c1-194">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="ee5c1-195">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="ee5c1-196">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="ee5c1-197">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="ee5c1-198">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="ee5c1-199">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="ee5c1-200">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="ee5c1-201">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="ee5c1-202">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-202">Create a manager authorization handler</span></span>

<span data-ttu-id="ee5c1-203">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="ee5c1-204">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="ee5c1-205">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="ee5c1-206">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="ee5c1-207">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="ee5c1-208">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="ee5c1-209">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="ee5c1-210">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-210">Register the authorization handlers</span></span>

<span data-ttu-id="ee5c1-211">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="ee5c1-212">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="ee5c1-213">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ee5c1-214">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="ee5c1-215">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="ee5c1-216">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="ee5c1-217">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="ee5c1-217">Support authorization</span></span>

<span data-ttu-id="ee5c1-218">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="ee5c1-219">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="ee5c1-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="ee5c1-220">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="ee5c1-221">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="ee5c1-222">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="ee5c1-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="ee5c1-223">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="ee5c1-224">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="ee5c1-225">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-225">The preceding code:</span></span>

* <span data-ttu-id="ee5c1-226">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="ee5c1-227">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="ee5c1-228">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="ee5c1-229">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-229">Update the CreateModel</span></span>

<span data-ttu-id="ee5c1-230">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="ee5c1-231">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="ee5c1-232">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="ee5c1-233">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="ee5c1-234">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-234">Update the IndexModel</span></span>

<span data-ttu-id="ee5c1-235">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="ee5c1-236">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-236">Update the EditModel</span></span>

<span data-ttu-id="ee5c1-237">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="ee5c1-238">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="ee5c1-239">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="ee5c1-240">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="ee5c1-241">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="ee5c1-242">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="ee5c1-243">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-243">Update the DeleteModel</span></span>

<span data-ttu-id="ee5c1-244">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="ee5c1-245">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="ee5c1-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="ee5c1-246">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="ee5c1-247">Inserte el servicio de autorización en el archivo *pages/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="ee5c1-248">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="ee5c1-249">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="ee5c1-250">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="ee5c1-251">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="ee5c1-252">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="ee5c1-253">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="ee5c1-254">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-254">Update Details</span></span>

<span data-ttu-id="ee5c1-255">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="ee5c1-256">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="ee5c1-257">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="ee5c1-257">Add or remove a user to a role</span></span>

<span data-ttu-id="ee5c1-258">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="ee5c1-259">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-259">Removing privileges from a user.</span></span> <span data-ttu-id="ee5c1-260">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="ee5c1-261">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="ee5c1-262">Diferencias entre desafío y prohibido</span><span class="sxs-lookup"><span data-stu-id="ee5c1-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="ee5c1-263">Esta aplicación establece la directiva predeterminada para [requerir usuarios autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="ee5c1-264">El código siguiente permite usuarios anónimos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-264">The following code allows anonymous users.</span></span> <span data-ttu-id="ee5c1-265">Los usuarios anónimos pueden mostrar las diferencias entre desafío y prohibido.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="ee5c1-266">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-266">In the preceding code:</span></span>

* <span data-ttu-id="ee5c1-267">Cuando el usuario **no** está autenticado, `ChallengeResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="ee5c1-268">Cuando `ChallengeResult` se devuelve, se redirige al usuario a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="ee5c1-269">Cuando el usuario está autenticado, pero no autorizado, `ForbidResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="ee5c1-270">Cuando `ForbidResult` se devuelve, se redirige al usuario a la página acceso denegado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="ee5c1-271">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="ee5c1-271">Test the completed app</span></span>

<span data-ttu-id="ee5c1-272">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="ee5c1-273">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="ee5c1-274">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="ee5c1-275">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="ee5c1-276">Si la aplicación tiene contactos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-276">If the app has contacts:</span></span>

* <span data-ttu-id="ee5c1-277">Elimine todos los registros de la `Contact` tabla.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="ee5c1-278">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="ee5c1-279">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="ee5c1-280">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="ee5c1-281">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="ee5c1-282">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-282">Verify the following operations:</span></span>

* <span data-ttu-id="ee5c1-283">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="ee5c1-284">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="ee5c1-285">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="ee5c1-286">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="ee5c1-287">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="ee5c1-288">Usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-288">User</span></span>                | <span data-ttu-id="ee5c1-289">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="ee5c1-289">Seeded by the app</span></span> | <span data-ttu-id="ee5c1-290">Opciones</span><span class="sxs-lookup"><span data-stu-id="ee5c1-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="ee5c1-291">No</span><span class="sxs-lookup"><span data-stu-id="ee5c1-291">No</span></span>                | <span data-ttu-id="ee5c1-292">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="ee5c1-293">Sí</span><span class="sxs-lookup"><span data-stu-id="ee5c1-293">Yes</span></span>               | <span data-ttu-id="ee5c1-294">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="ee5c1-295">Sí</span><span class="sxs-lookup"><span data-stu-id="ee5c1-295">Yes</span></span>               | <span data-ttu-id="ee5c1-296">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="ee5c1-297">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="ee5c1-298">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="ee5c1-299">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="ee5c1-300">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="ee5c1-300">Create the starter app</span></span>

* <span data-ttu-id="ee5c1-301">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="ee5c1-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="ee5c1-302">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="ee5c1-303">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="ee5c1-304">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="ee5c1-305">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="ee5c1-306">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="ee5c1-307">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="ee5c1-308">Si experimenta un error con el `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema de github](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="ee5c1-309">Actualice el delimitador **ContactManager** en el archivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="ee5c1-310">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="ee5c1-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="ee5c1-311">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-311">Seed the database</span></span>

<span data-ttu-id="ee5c1-312">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) a la carpeta de *datos* :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="ee5c1-313">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="ee5c1-314">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-314">Test that the app seeded the database.</span></span> <span data-ttu-id="ee5c1-315">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="ee5c1-316">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="ee5c1-317">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="ee5c1-318">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-318">There are three security groups:</span></span>

* <span data-ttu-id="ee5c1-319">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="ee5c1-320">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="ee5c1-321">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="ee5c1-322">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="ee5c1-323">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="ee5c1-324">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="ee5c1-325">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="ee5c1-326">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="ee5c1-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="ee5c1-328">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="ee5c1-330">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-330">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="ee5c1-332">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="ee5c1-333">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="ee5c1-335">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-335">The administrator has all privileges.</span></span> <span data-ttu-id="ee5c1-336">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="ee5c1-337">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="ee5c1-338">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="ee5c1-339">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="ee5c1-340">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="ee5c1-341">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ee5c1-342">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-342">Prerequisites</span></span>

<span data-ttu-id="ee5c1-343">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-343">This tutorial is advanced.</span></span> <span data-ttu-id="ee5c1-344">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-344">You should be familiar with:</span></span>

* [<span data-ttu-id="ee5c1-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee5c1-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="ee5c1-346">Autenticación</span><span class="sxs-lookup"><span data-stu-id="ee5c1-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ee5c1-347">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="ee5c1-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="ee5c1-348">Autorización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="ee5c1-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ee5c1-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="ee5c1-350">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="ee5c1-350">The starter and completed app</span></span>

<span data-ttu-id="ee5c1-351">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="ee5c1-352">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="ee5c1-353">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="ee5c1-353">The starter app</span></span>

<span data-ttu-id="ee5c1-354">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="ee5c1-355">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="ee5c1-356">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-356">Secure user data</span></span>

<span data-ttu-id="ee5c1-357">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="ee5c1-358">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="ee5c1-359">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-359">Tie the contact data to the user</span></span>

<span data-ttu-id="ee5c1-360">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="ee5c1-361">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="ee5c1-362">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="ee5c1-363">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="ee5c1-364">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="ee5c1-365">Agregar servicios de función a Identity</span><span class="sxs-lookup"><span data-stu-id="ee5c1-365">Add Role services to Identity</span></span>

<span data-ttu-id="ee5c1-366">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="ee5c1-367">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="ee5c1-367">Require authenticated users</span></span>

<span data-ttu-id="ee5c1-368">Establezca la Directiva de autenticación predeterminada para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="ee5c1-369">Puede rechazar la autenticación en el nivel de la Razor página, el controlador o el método de acción con el `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="ee5c1-370">La configuración de la Directiva de autenticación predeterminada para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="ee5c1-371">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="ee5c1-372">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las páginas index, about y Contact para que los usuarios anónimos puedan obtener información sobre el sitio antes de registrarse.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="ee5c1-373">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="ee5c1-373">Configure the test account</span></span>

<span data-ttu-id="ee5c1-374">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="ee5c1-375">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="ee5c1-376">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="ee5c1-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="ee5c1-377">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="ee5c1-378">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="ee5c1-379">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="ee5c1-380">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="ee5c1-381">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="ee5c1-382">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="ee5c1-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="ee5c1-383">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="ee5c1-384">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="ee5c1-385">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="ee5c1-386">Cree una carpeta de *autorización* y cree una `ContactIsOwnerAuthorizationHandler` clase en ella.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="ee5c1-387">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="ee5c1-388">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="ee5c1-389">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="ee5c1-390">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="ee5c1-391">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="ee5c1-392">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="ee5c1-393">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="ee5c1-394">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="ee5c1-395">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="ee5c1-396">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-396">Create a manager authorization handler</span></span>

<span data-ttu-id="ee5c1-397">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="ee5c1-398">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="ee5c1-399">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="ee5c1-400">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="ee5c1-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="ee5c1-401">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="ee5c1-402">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="ee5c1-403">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="ee5c1-404">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-404">Register the authorization handlers</span></span>

<span data-ttu-id="ee5c1-405">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="ee5c1-406">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="ee5c1-407">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ee5c1-408">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="ee5c1-409">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="ee5c1-410">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="ee5c1-411">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="ee5c1-411">Support authorization</span></span>

<span data-ttu-id="ee5c1-412">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="ee5c1-413">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="ee5c1-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="ee5c1-414">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="ee5c1-415">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="ee5c1-416">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="ee5c1-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="ee5c1-417">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="ee5c1-418">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="ee5c1-419">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-419">The preceding code:</span></span>

* <span data-ttu-id="ee5c1-420">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="ee5c1-421">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="ee5c1-422">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="ee5c1-423">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-423">Update the CreateModel</span></span>

<span data-ttu-id="ee5c1-424">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="ee5c1-425">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="ee5c1-426">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="ee5c1-427">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="ee5c1-428">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-428">Update the IndexModel</span></span>

<span data-ttu-id="ee5c1-429">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="ee5c1-430">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-430">Update the EditModel</span></span>

<span data-ttu-id="ee5c1-431">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="ee5c1-432">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="ee5c1-433">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="ee5c1-434">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="ee5c1-435">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="ee5c1-436">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="ee5c1-437">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="ee5c1-437">Update the DeleteModel</span></span>

<span data-ttu-id="ee5c1-438">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="ee5c1-439">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="ee5c1-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="ee5c1-440">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="ee5c1-441">Inserte el servicio de autorización en el archivo *views/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="ee5c1-442">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="ee5c1-443">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="ee5c1-444">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="ee5c1-445">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="ee5c1-446">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="ee5c1-447">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="ee5c1-448">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="ee5c1-448">Update Details</span></span>

<span data-ttu-id="ee5c1-449">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="ee5c1-450">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="ee5c1-451">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="ee5c1-451">Add or remove a user to a role</span></span>

<span data-ttu-id="ee5c1-452">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="ee5c1-453">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-453">Removing privileges from a user.</span></span> <span data-ttu-id="ee5c1-454">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="ee5c1-455">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="ee5c1-456">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="ee5c1-456">Test the completed app</span></span>

<span data-ttu-id="ee5c1-457">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="ee5c1-458">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="ee5c1-459">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="ee5c1-460">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="ee5c1-461">Quitar y actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="ee5c1-462">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="ee5c1-463">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="ee5c1-464">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="ee5c1-465">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="ee5c1-466">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-466">Verify the following operations:</span></span>

* <span data-ttu-id="ee5c1-467">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="ee5c1-468">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="ee5c1-469">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="ee5c1-470">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="ee5c1-471">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="ee5c1-472">Usuario</span><span class="sxs-lookup"><span data-stu-id="ee5c1-472">User</span></span>                | <span data-ttu-id="ee5c1-473">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="ee5c1-473">Seeded by the app</span></span> | <span data-ttu-id="ee5c1-474">Opciones</span><span class="sxs-lookup"><span data-stu-id="ee5c1-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="ee5c1-475">No</span><span class="sxs-lookup"><span data-stu-id="ee5c1-475">No</span></span>                | <span data-ttu-id="ee5c1-476">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="ee5c1-477">Sí</span><span class="sxs-lookup"><span data-stu-id="ee5c1-477">Yes</span></span>               | <span data-ttu-id="ee5c1-478">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="ee5c1-479">Sí</span><span class="sxs-lookup"><span data-stu-id="ee5c1-479">Yes</span></span>               | <span data-ttu-id="ee5c1-480">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="ee5c1-481">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="ee5c1-482">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="ee5c1-483">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="ee5c1-484">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="ee5c1-484">Create the starter app</span></span>

* <span data-ttu-id="ee5c1-485">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="ee5c1-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="ee5c1-486">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="ee5c1-487">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="ee5c1-488">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="ee5c1-489">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="ee5c1-490">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="ee5c1-491">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="ee5c1-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="ee5c1-492">Actualice el delimitador **ContactManager** en el archivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="ee5c1-493">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="ee5c1-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="ee5c1-494">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="ee5c1-494">Seed the database</span></span>

<span data-ttu-id="ee5c1-495">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) a la carpeta de *datos* .</span><span class="sxs-lookup"><span data-stu-id="ee5c1-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="ee5c1-496">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="ee5c1-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="ee5c1-497">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-497">Test that the app seeded the database.</span></span> <span data-ttu-id="ee5c1-498">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="ee5c1-499">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ee5c1-499">Additional resources</span></span>

* [<span data-ttu-id="ee5c1-500">Creación de una aplicación .NET Core y SQL Database en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ee5c1-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="ee5c1-501">[ASP.net Core laboratorio de autorización](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="ee5c1-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="ee5c1-502">En este laboratorio se incluyen más detalles sobre las características de seguridad que se presentan en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="ee5c1-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="ee5c1-503">Autorización personalizada basada en directivas</span><span class="sxs-lookup"><span data-stu-id="ee5c1-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
