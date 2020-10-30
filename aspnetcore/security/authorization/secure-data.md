---
title: Creación de una aplicación ASP.NET Core con datos de usuario protegidos por autorización
author: rick-anderson
description: 'Obtenga información sobre cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización. Incluye HTTPS, autenticación, seguridad, :::no-loc(ASP.NET Core Identity)::: .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051946"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="81af5-104">Creación de una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización</span><span class="sxs-lookup"><span data-stu-id="81af5-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="81af5-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="81af5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="81af5-106">Vea [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="81af5-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81af5-107">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="81af5-108">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="81af5-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="81af5-109">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="81af5-109">There are three security groups:</span></span>

* <span data-ttu-id="81af5-110">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="81af5-111">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="81af5-112">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="81af5-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="81af5-113">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="81af5-114">Las imágenes de este documento no coinciden exactamente con las plantillas más recientes.</span><span class="sxs-lookup"><span data-stu-id="81af5-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="81af5-115">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="81af5-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="81af5-116">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="81af5-117">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="81af5-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="81af5-118">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="81af5-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="81af5-120">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="81af5-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="81af5-122">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="81af5-122">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="81af5-124">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="81af5-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="81af5-125">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="81af5-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="81af5-127">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="81af5-127">The administrator has all privileges.</span></span> <span data-ttu-id="81af5-128">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="81af5-129">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="81af5-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="81af5-130">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="81af5-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="81af5-131">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="81af5-132">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="81af5-133">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81af5-134">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="81af5-134">Prerequisites</span></span>

<span data-ttu-id="81af5-135">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="81af5-135">This tutorial is advanced.</span></span> <span data-ttu-id="81af5-136">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="81af5-136">You should be familiar with:</span></span>

* [<span data-ttu-id="81af5-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81af5-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="81af5-138">Autenticación</span><span class="sxs-lookup"><span data-stu-id="81af5-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="81af5-139">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="81af5-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="81af5-140">Autorización</span><span class="sxs-lookup"><span data-stu-id="81af5-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="81af5-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="81af5-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="81af5-142">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="81af5-142">The starter and completed app</span></span>

<span data-ttu-id="81af5-143">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="81af5-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="81af5-144">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="81af5-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="81af5-145">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="81af5-145">The starter app</span></span>

<span data-ttu-id="81af5-146">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="81af5-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="81af5-147">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="81af5-148">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-148">Secure user data</span></span>

<span data-ttu-id="81af5-149">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="81af5-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="81af5-150">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="81af5-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="81af5-151">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-151">Tie the contact data to the user</span></span>

<span data-ttu-id="81af5-152">Use el [:::no-loc(Identity):::](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="81af5-152">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="81af5-153">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="81af5-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="81af5-154">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [:::no-loc(Identity):::](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="81af5-155">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="81af5-156">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="81af5-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="81af5-157">Agregar servicios de función a :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="81af5-157">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="81af5-158">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="81af5-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="81af5-159">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="81af5-159">Require authenticated users</span></span>

<span data-ttu-id="81af5-160">Establezca la Directiva de autenticación de reserva para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="81af5-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="81af5-161">El código resaltado anterior establece la [Directiva de autenticación de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="81af5-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="81af5-162">La Directiva de autenticación de reserva requiere \* *_All_* _ usuarios para autenticarse, excepto :::no-loc(Razor)::: las páginas, los controladores o los métodos de acción con un atributo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="81af5-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for :::no-loc(Razor)::: Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="81af5-163">Por ejemplo, :::no-loc(Razor)::: páginas, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autenticación aplicado en lugar de la Directiva de autenticación de reserva.</span><span class="sxs-lookup"><span data-stu-id="81af5-163">For example, :::no-loc(Razor)::: Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="81af5-164">La Directiva de autenticación de reserva:</span><span class="sxs-lookup"><span data-stu-id="81af5-164">The fallback authentication policy:</span></span>

<span data-ttu-id="81af5-165">_ Se aplica a todas las solicitudes que no especifican explícitamente una directiva de autenticación.</span><span class="sxs-lookup"><span data-stu-id="81af5-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="81af5-166">En el caso de las solicitudes atendidas por el enrutamiento del punto de conexión, esto incluiría cualquier punto de conexión que no especifique un atributo de autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="81af5-167">En el caso de las solicitudes atendidas por otro middleware después del middleware de autorización, como [archivos estáticos](xref:fundamentals/static-files), esto aplicaría la Directiva a todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="81af5-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="81af5-168">La configuración de la Directiva de autenticación de reserva para requerir que los usuarios se autentiquen protege :::no-loc(Razor)::: las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="81af5-168">Setting the fallback authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="81af5-169">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores :::no-loc(Razor)::: para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="81af5-169">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="81af5-170">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> clase también contiene <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="81af5-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="81af5-171">`DefaultPolicy`Es la Directiva que se usa con el `[Authorize]` atributo cuando no se especifica ninguna directiva.</span><span class="sxs-lookup"><span data-stu-id="81af5-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="81af5-172">`[Authorize]` no contiene una directiva con nombre, a diferencia de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="81af5-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="81af5-173">Para obtener más información acerca de las directivas, vea <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="81af5-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="81af5-174">Una forma alternativa para que las páginas y los controladores de MVC :::no-loc(Razor)::: requieran que todos los usuarios se autentiquen es agregar un filtro de autorización:</span><span class="sxs-lookup"><span data-stu-id="81af5-174">An alternative way for MVC controllers and :::no-loc(Razor)::: Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="81af5-175">El código anterior usa un filtro de autorización y el establecimiento de la Directiva de reserva usa el enrutamiento del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="81af5-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="81af5-176">La configuración de la Directiva de reserva es la manera preferida de requerir la autenticación de todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="81af5-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="81af5-177">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las `Index` `Privacy` páginas y para que los usuarios anónimos puedan obtener información sobre el sitio antes de que se registren:</span><span class="sxs-lookup"><span data-stu-id="81af5-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="81af5-178">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="81af5-178">Configure the test account</span></span>

<span data-ttu-id="81af5-179">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="81af5-180">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="81af5-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="81af5-181">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS* ):</span><span class="sxs-lookup"><span data-stu-id="81af5-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="81af5-182">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="81af5-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="81af5-183">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="81af5-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="81af5-184">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="81af5-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="81af5-185">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="81af5-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="81af5-186">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="81af5-187">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="81af5-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="81af5-188">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="81af5-189">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="81af5-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="81af5-190">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="81af5-191">Cree una `ContactIsOwnerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="81af5-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="81af5-192">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="81af5-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="81af5-193">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="81af5-194">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="81af5-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="81af5-195">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="81af5-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="81af5-196">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="81af5-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="81af5-197">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="81af5-198">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="81af5-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="81af5-199">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="81af5-200">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="81af5-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="81af5-201">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-201">Create a manager authorization handler</span></span>

<span data-ttu-id="81af5-202">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="81af5-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="81af5-203">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="81af5-204">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="81af5-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="81af5-205">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="81af5-206">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="81af5-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="81af5-207">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="81af5-208">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="81af5-209">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="81af5-209">Register the authorization handlers</span></span>

<span data-ttu-id="81af5-210">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="81af5-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="81af5-211">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [:::no-loc(Identity):::](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="81af5-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="81af5-212">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="81af5-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="81af5-213">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="81af5-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="81af5-214">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="81af5-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="81af5-215">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="81af5-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="81af5-216">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="81af5-216">Support authorization</span></span>

<span data-ttu-id="81af5-217">En esta sección, actualizará las :::no-loc(Razor)::: páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-217">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="81af5-218">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="81af5-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="81af5-219">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="81af5-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="81af5-220">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="81af5-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="81af5-221">Crear una clase base para las páginas de contactos :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="81af5-221">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="81af5-222">Cree una clase base que contenga los servicios usados en las :::no-loc(Razor)::: páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-222">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="81af5-223">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="81af5-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="81af5-224">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="81af5-224">The preceding code:</span></span>

* <span data-ttu-id="81af5-225">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="81af5-226">Agrega el :::no-loc(Identity)::: `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="81af5-226">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="81af5-227">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="81af5-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="81af5-228">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="81af5-228">Update the CreateModel</span></span>

<span data-ttu-id="81af5-229">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="81af5-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="81af5-230">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="81af5-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="81af5-231">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="81af5-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="81af5-232">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="81af5-233">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="81af5-233">Update the IndexModel</span></span>

<span data-ttu-id="81af5-234">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="81af5-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="81af5-235">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="81af5-235">Update the EditModel</span></span>

<span data-ttu-id="81af5-236">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="81af5-237">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="81af5-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="81af5-238">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="81af5-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="81af5-239">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="81af5-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="81af5-240">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="81af5-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="81af5-241">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="81af5-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="81af5-242">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="81af5-242">Update the DeleteModel</span></span>

<span data-ttu-id="81af5-243">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="81af5-244">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="81af5-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="81af5-245">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="81af5-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="81af5-246">Inserte el servicio de autorización en el archivo *pages/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="81af5-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="81af5-247">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="81af5-248">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="81af5-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="81af5-249">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="81af5-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="81af5-250">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="81af5-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="81af5-251">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="81af5-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="81af5-252">La :::no-loc(Razor)::: página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-252">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="81af5-253">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="81af5-253">Update Details</span></span>

<span data-ttu-id="81af5-254">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="81af5-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="81af5-255">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="81af5-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="81af5-256">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="81af5-256">Add or remove a user to a role</span></span>

<span data-ttu-id="81af5-257">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="81af5-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="81af5-258">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="81af5-258">Removing privileges from a user.</span></span> <span data-ttu-id="81af5-259">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="81af5-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="81af5-260">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="81af5-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="81af5-261">Diferencias entre desafío y prohibido</span><span class="sxs-lookup"><span data-stu-id="81af5-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="81af5-262">Esta aplicación establece la directiva predeterminada para [requerir usuarios autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="81af5-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="81af5-263">El código siguiente permite usuarios anónimos.</span><span class="sxs-lookup"><span data-stu-id="81af5-263">The following code allows anonymous users.</span></span> <span data-ttu-id="81af5-264">Los usuarios anónimos pueden mostrar las diferencias entre desafío y prohibido.</span><span class="sxs-lookup"><span data-stu-id="81af5-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="81af5-265">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="81af5-265">In the preceding code:</span></span>

* <span data-ttu-id="81af5-266">Cuando el usuario **no** está autenticado, `ChallengeResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="81af5-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="81af5-267">Cuando `ChallengeResult` se devuelve, se redirige al usuario a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="81af5-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="81af5-268">Cuando el usuario está autenticado, pero no autorizado, `ForbidResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="81af5-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="81af5-269">Cuando `ForbidResult` se devuelve, se redirige al usuario a la página acceso denegado.</span><span class="sxs-lookup"><span data-stu-id="81af5-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="81af5-270">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="81af5-270">Test the completed app</span></span>

<span data-ttu-id="81af5-271">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="81af5-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="81af5-272">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="81af5-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="81af5-273">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="81af5-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="81af5-274">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="81af5-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="81af5-275">Si la aplicación tiene contactos:</span><span class="sxs-lookup"><span data-stu-id="81af5-275">If the app has contacts:</span></span>

* <span data-ttu-id="81af5-276">Elimine todos los registros de la `Contact` tabla.</span><span class="sxs-lookup"><span data-stu-id="81af5-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="81af5-277">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="81af5-278">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="81af5-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="81af5-279">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="81af5-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="81af5-280">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="81af5-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="81af5-281">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="81af5-281">Verify the following operations:</span></span>

* <span data-ttu-id="81af5-282">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="81af5-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="81af5-283">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="81af5-284">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="81af5-285">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="81af5-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="81af5-286">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="81af5-287">Usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-287">User</span></span>                | <span data-ttu-id="81af5-288">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="81af5-288">Seeded by the app</span></span> | <span data-ttu-id="81af5-289">Opciones</span><span class="sxs-lookup"><span data-stu-id="81af5-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="81af5-290">No</span><span class="sxs-lookup"><span data-stu-id="81af5-290">No</span></span>                | <span data-ttu-id="81af5-291">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="81af5-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="81af5-292">Sí</span><span class="sxs-lookup"><span data-stu-id="81af5-292">Yes</span></span>               | <span data-ttu-id="81af5-293">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="81af5-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="81af5-294">Sí</span><span class="sxs-lookup"><span data-stu-id="81af5-294">Yes</span></span>               | <span data-ttu-id="81af5-295">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="81af5-296">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="81af5-297">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="81af5-298">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="81af5-299">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="81af5-299">Create the starter app</span></span>

* <span data-ttu-id="81af5-300">Crear una :::no-loc(Razor)::: aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="81af5-300">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="81af5-301">Cree la aplicación con **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="81af5-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="81af5-302">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="81af5-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="81af5-303">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="81af5-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="81af5-304">Agregue *Models/contact. CS* :</span><span class="sxs-lookup"><span data-stu-id="81af5-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="81af5-305">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="81af5-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="81af5-306">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="81af5-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="81af5-307">Si experimenta un error con el `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema de github](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="81af5-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="81af5-308">Actualice el delimitador **ContactManager** en el archivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="81af5-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="81af5-309">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="81af5-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="81af5-310">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="81af5-310">Seed the database</span></span>

<span data-ttu-id="81af5-311">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) a la carpeta de *datos* :</span><span class="sxs-lookup"><span data-stu-id="81af5-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="81af5-312">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="81af5-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="81af5-313">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-313">Test that the app seeded the database.</span></span> <span data-ttu-id="81af5-314">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="81af5-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="81af5-315">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="81af5-316">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="81af5-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="81af5-317">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="81af5-317">There are three security groups:</span></span>

* <span data-ttu-id="81af5-318">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="81af5-319">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="81af5-320">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="81af5-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="81af5-321">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="81af5-322">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="81af5-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="81af5-323">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="81af5-324">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="81af5-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="81af5-325">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="81af5-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="81af5-327">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="81af5-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="81af5-329">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="81af5-329">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="81af5-331">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="81af5-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="81af5-332">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="81af5-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="81af5-334">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="81af5-334">The administrator has all privileges.</span></span> <span data-ttu-id="81af5-335">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="81af5-336">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="81af5-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="81af5-337">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="81af5-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="81af5-338">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="81af5-339">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="81af5-340">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81af5-341">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="81af5-341">Prerequisites</span></span>

<span data-ttu-id="81af5-342">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="81af5-342">This tutorial is advanced.</span></span> <span data-ttu-id="81af5-343">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="81af5-343">You should be familiar with:</span></span>

* [<span data-ttu-id="81af5-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81af5-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="81af5-345">Autenticación</span><span class="sxs-lookup"><span data-stu-id="81af5-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="81af5-346">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="81af5-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="81af5-347">Autorización</span><span class="sxs-lookup"><span data-stu-id="81af5-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="81af5-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="81af5-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="81af5-349">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="81af5-349">The starter and completed app</span></span>

<span data-ttu-id="81af5-350">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="81af5-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="81af5-351">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="81af5-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="81af5-352">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="81af5-352">The starter app</span></span>

<span data-ttu-id="81af5-353">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="81af5-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="81af5-354">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="81af5-355">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-355">Secure user data</span></span>

<span data-ttu-id="81af5-356">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="81af5-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="81af5-357">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="81af5-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="81af5-358">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-358">Tie the contact data to the user</span></span>

<span data-ttu-id="81af5-359">Use el [:::no-loc(Identity):::](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="81af5-359">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="81af5-360">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="81af5-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="81af5-361">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [:::no-loc(Identity):::](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="81af5-362">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="81af5-363">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="81af5-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="81af5-364">Agregar servicios de función a :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="81af5-364">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="81af5-365">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="81af5-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="81af5-366">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="81af5-366">Require authenticated users</span></span>

<span data-ttu-id="81af5-367">Establezca la Directiva de autenticación predeterminada para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="81af5-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="81af5-368">Puede rechazar la autenticación en el nivel de la :::no-loc(Razor)::: página, el controlador o el método de acción con el `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="81af5-368">You can opt out of authentication at the :::no-loc(Razor)::: Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="81af5-369">La configuración de la Directiva de autenticación predeterminada para requerir que los usuarios se autentiquen protege :::no-loc(Razor)::: las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="81af5-369">Setting the default authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="81af5-370">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores :::no-loc(Razor)::: para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="81af5-370">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="81af5-371">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las páginas index, about y Contact para que los usuarios anónimos puedan obtener información sobre el sitio antes de registrarse.</span><span class="sxs-lookup"><span data-stu-id="81af5-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="81af5-372">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="81af5-372">Configure the test account</span></span>

<span data-ttu-id="81af5-373">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="81af5-374">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="81af5-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="81af5-375">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS* ):</span><span class="sxs-lookup"><span data-stu-id="81af5-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="81af5-376">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="81af5-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="81af5-377">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="81af5-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="81af5-378">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="81af5-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="81af5-379">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="81af5-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="81af5-380">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="81af5-381">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="81af5-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="81af5-382">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="81af5-383">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="81af5-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="81af5-384">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="81af5-385">Cree una carpeta de *autorización* y cree una `ContactIsOwnerAuthorizationHandler` clase en ella.</span><span class="sxs-lookup"><span data-stu-id="81af5-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="81af5-386">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="81af5-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="81af5-387">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="81af5-388">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="81af5-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="81af5-389">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="81af5-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="81af5-390">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="81af5-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="81af5-391">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="81af5-392">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="81af5-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="81af5-393">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="81af5-394">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="81af5-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="81af5-395">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-395">Create a manager authorization handler</span></span>

<span data-ttu-id="81af5-396">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="81af5-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="81af5-397">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="81af5-398">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="81af5-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="81af5-399">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="81af5-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="81af5-400">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="81af5-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="81af5-401">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="81af5-402">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="81af5-403">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="81af5-403">Register the authorization handlers</span></span>

<span data-ttu-id="81af5-404">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="81af5-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="81af5-405">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [:::no-loc(Identity):::](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="81af5-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="81af5-406">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="81af5-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="81af5-407">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="81af5-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="81af5-408">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="81af5-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="81af5-409">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="81af5-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="81af5-410">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="81af5-410">Support authorization</span></span>

<span data-ttu-id="81af5-411">En esta sección, actualizará las :::no-loc(Razor)::: páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-411">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="81af5-412">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="81af5-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="81af5-413">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="81af5-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="81af5-414">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="81af5-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="81af5-415">Crear una clase base para las páginas de contactos :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="81af5-415">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="81af5-416">Cree una clase base que contenga los servicios usados en las :::no-loc(Razor)::: páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-416">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="81af5-417">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="81af5-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="81af5-418">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="81af5-418">The preceding code:</span></span>

* <span data-ttu-id="81af5-419">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="81af5-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="81af5-420">Agrega el :::no-loc(Identity)::: `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="81af5-420">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="81af5-421">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="81af5-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="81af5-422">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="81af5-422">Update the CreateModel</span></span>

<span data-ttu-id="81af5-423">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="81af5-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="81af5-424">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="81af5-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="81af5-425">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="81af5-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="81af5-426">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="81af5-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="81af5-427">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="81af5-427">Update the IndexModel</span></span>

<span data-ttu-id="81af5-428">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="81af5-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="81af5-429">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="81af5-429">Update the EditModel</span></span>

<span data-ttu-id="81af5-430">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="81af5-431">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="81af5-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="81af5-432">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="81af5-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="81af5-433">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="81af5-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="81af5-434">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="81af5-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="81af5-435">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="81af5-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="81af5-436">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="81af5-436">Update the DeleteModel</span></span>

<span data-ttu-id="81af5-437">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="81af5-438">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="81af5-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="81af5-439">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="81af5-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="81af5-440">Inserte el servicio de autorización en el archivo *views/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="81af5-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="81af5-441">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="81af5-442">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="81af5-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="81af5-443">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="81af5-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="81af5-444">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="81af5-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="81af5-445">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="81af5-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="81af5-446">La :::no-loc(Razor)::: página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-446">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="81af5-447">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="81af5-447">Update Details</span></span>

<span data-ttu-id="81af5-448">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="81af5-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="81af5-449">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="81af5-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="81af5-450">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="81af5-450">Add or remove a user to a role</span></span>

<span data-ttu-id="81af5-451">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="81af5-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="81af5-452">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="81af5-452">Removing privileges from a user.</span></span> <span data-ttu-id="81af5-453">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="81af5-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="81af5-454">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="81af5-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="81af5-455">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="81af5-455">Test the completed app</span></span>

<span data-ttu-id="81af5-456">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="81af5-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="81af5-457">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="81af5-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="81af5-458">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="81af5-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="81af5-459">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="81af5-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="81af5-460">Quitar y actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="81af5-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="81af5-461">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="81af5-462">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="81af5-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="81af5-463">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="81af5-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="81af5-464">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="81af5-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="81af5-465">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="81af5-465">Verify the following operations:</span></span>

* <span data-ttu-id="81af5-466">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="81af5-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="81af5-467">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="81af5-468">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="81af5-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="81af5-469">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="81af5-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="81af5-470">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="81af5-471">Usuario</span><span class="sxs-lookup"><span data-stu-id="81af5-471">User</span></span>                | <span data-ttu-id="81af5-472">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="81af5-472">Seeded by the app</span></span> | <span data-ttu-id="81af5-473">Opciones</span><span class="sxs-lookup"><span data-stu-id="81af5-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="81af5-474">No</span><span class="sxs-lookup"><span data-stu-id="81af5-474">No</span></span>                | <span data-ttu-id="81af5-475">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="81af5-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="81af5-476">Sí</span><span class="sxs-lookup"><span data-stu-id="81af5-476">Yes</span></span>               | <span data-ttu-id="81af5-477">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="81af5-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="81af5-478">Sí</span><span class="sxs-lookup"><span data-stu-id="81af5-478">Yes</span></span>               | <span data-ttu-id="81af5-479">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="81af5-480">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="81af5-481">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="81af5-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="81af5-482">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="81af5-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="81af5-483">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="81af5-483">Create the starter app</span></span>

* <span data-ttu-id="81af5-484">Crear una :::no-loc(Razor)::: aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="81af5-484">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="81af5-485">Cree la aplicación con **cuentas de usuario individuales** .</span><span class="sxs-lookup"><span data-stu-id="81af5-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="81af5-486">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="81af5-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="81af5-487">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="81af5-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="81af5-488">Agregue *Models/contact. CS* :</span><span class="sxs-lookup"><span data-stu-id="81af5-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="81af5-489">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="81af5-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="81af5-490">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="81af5-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="81af5-491">Actualice el delimitador **ContactManager** en el archivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="81af5-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="81af5-492">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="81af5-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="81af5-493">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="81af5-493">Seed the database</span></span>

<span data-ttu-id="81af5-494">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) a la carpeta de *datos* .</span><span class="sxs-lookup"><span data-stu-id="81af5-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="81af5-495">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="81af5-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="81af5-496">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="81af5-496">Test that the app seeded the database.</span></span> <span data-ttu-id="81af5-497">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="81af5-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="81af5-498">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="81af5-498">Additional resources</span></span>

* [<span data-ttu-id="81af5-499">Creación de una aplicación .NET Core y SQL Database en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="81af5-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="81af5-500">[ASP.net Core laboratorio de autorización](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="81af5-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="81af5-501">En este laboratorio se incluyen más detalles sobre las características de seguridad que se presentan en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="81af5-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="81af5-502">Autorización personalizada basada en directivas</span><span class="sxs-lookup"><span data-stu-id="81af5-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
