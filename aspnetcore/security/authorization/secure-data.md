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
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689310"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="1a4a5-104">Creación de una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="1a4a5-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="1a4a5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="1a4a5-106">Vea [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="1a4a5-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a4a5-107">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1a4a5-108">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1a4a5-109">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-109">There are three security groups:</span></span>

* <span data-ttu-id="1a4a5-110">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1a4a5-111">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1a4a5-112">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1a4a5-113">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1a4a5-114">Las imágenes de este documento no coinciden exactamente con las plantillas más recientes.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="1a4a5-115">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1a4a5-116">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1a4a5-117">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1a4a5-118">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="1a4a5-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="1a4a5-120">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="1a4a5-122">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-122">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="1a4a5-124">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1a4a5-125">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="1a4a5-127">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-127">The administrator has all privileges.</span></span> <span data-ttu-id="1a4a5-128">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1a4a5-129">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1a4a5-130">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1a4a5-131">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1a4a5-132">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1a4a5-133">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a4a5-134">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-134">Prerequisites</span></span>

<span data-ttu-id="1a4a5-135">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-135">This tutorial is advanced.</span></span> <span data-ttu-id="1a4a5-136">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-136">You should be familiar with:</span></span>

* [<span data-ttu-id="1a4a5-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a4a5-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1a4a5-138">Autenticación</span><span class="sxs-lookup"><span data-stu-id="1a4a5-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1a4a5-139">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="1a4a5-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1a4a5-140">Autorización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1a4a5-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1a4a5-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1a4a5-142">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="1a4a5-142">The starter and completed app</span></span>

<span data-ttu-id="1a4a5-143">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1a4a5-144">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1a4a5-145">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="1a4a5-145">The starter app</span></span>

<span data-ttu-id="1a4a5-146">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1a4a5-147">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="1a4a5-148">Para crear la aplicación de inicio, consulte [crear la aplicación de inicio](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1a4a5-149">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-149">Secure user data</span></span>

<span data-ttu-id="1a4a5-150">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1a4a5-151">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1a4a5-152">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-152">Tie the contact data to the user</span></span>

<span data-ttu-id="1a4a5-153">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1a4a5-154">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1a4a5-155">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1a4a5-156">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1a4a5-157">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1a4a5-158">Agregar servicios de función a Identity</span><span class="sxs-lookup"><span data-stu-id="1a4a5-158">Add Role services to Identity</span></span>

<span data-ttu-id="1a4a5-159">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="1a4a5-160">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="1a4a5-160">Require authenticated users</span></span>

<span data-ttu-id="1a4a5-161">Establezca la Directiva de autenticación de reserva para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="1a4a5-162">El código resaltado anterior establece la [Directiva de autenticación de reserva](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="1a4a5-163">La Directiva de autenticación de reserva requiere \**_All_* _ usuarios para autenticarse, excepto Razor las páginas, los controladores o los métodos de acción con un atributo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="1a4a5-164">Por ejemplo, Razor páginas, controladores o métodos de acción con `[AllowAnonymous]` o `[Authorize(PolicyName="MyPolicy")]` usan el atributo de autenticación aplicado en lugar de la Directiva de autenticación de reserva.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="1a4a5-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> agrega <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> a la instancia actual, lo que exige que se autentique el usuario actual.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="1a4a5-166">La Directiva de autenticación de reserva:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-166">The fallback authentication policy:</span></span>

<span data-ttu-id="1a4a5-167">_ Se aplica a todas las solicitudes que no especifican explícitamente una directiva de autenticación.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="1a4a5-168">En el caso de las solicitudes atendidas por el enrutamiento del punto de conexión, esto incluiría cualquier punto de conexión que no especifique un atributo de autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="1a4a5-169">En el caso de las solicitudes atendidas por otro middleware después del middleware de autorización, como [archivos estáticos](xref:fundamentals/static-files), esto aplicaría la Directiva a todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="1a4a5-170">La configuración de la Directiva de autenticación de reserva para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1a4a5-171">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1a4a5-172">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> clase también contiene <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1a4a5-173">`DefaultPolicy`Es la Directiva que se usa con el `[Authorize]` atributo cuando no se especifica ninguna directiva.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="1a4a5-174">`[Authorize]` no contiene una directiva con nombre, a diferencia de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="1a4a5-175">Para obtener más información acerca de las directivas, vea <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="1a4a5-176">Una forma alternativa para que las páginas y los controladores de MVC Razor requieran que todos los usuarios se autentiquen es agregar un filtro de autorización:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="1a4a5-177">El código anterior usa un filtro de autorización y el establecimiento de la Directiva de reserva usa el enrutamiento del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="1a4a5-178">La configuración de la Directiva de reserva es la manera preferida de requerir la autenticación de todos los usuarios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="1a4a5-179">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las `Index` `Privacy` páginas y para que los usuarios anónimos puedan obtener información sobre el sitio antes de que se registren:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1a4a5-180">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="1a4a5-180">Configure the test account</span></span>

<span data-ttu-id="1a4a5-181">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1a4a5-182">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1a4a5-183">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="1a4a5-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1a4a5-184">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1a4a5-185">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1a4a5-186">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1a4a5-187">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1a4a5-188">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1a4a5-189">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="1a4a5-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1a4a5-190">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1a4a5-191">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1a4a5-192">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1a4a5-193">Cree una `ContactIsOwnerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1a4a5-194">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1a4a5-195">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1a4a5-196">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="1a4a5-197">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1a4a5-198">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1a4a5-199">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1a4a5-200">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1a4a5-201">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1a4a5-202">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1a4a5-203">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-203">Create a manager authorization handler</span></span>

<span data-ttu-id="1a4a5-204">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1a4a5-205">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1a4a5-206">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1a4a5-207">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="1a4a5-208">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1a4a5-209">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1a4a5-210">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1a4a5-211">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-211">Register the authorization handlers</span></span>

<span data-ttu-id="1a4a5-212">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1a4a5-213">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1a4a5-214">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1a4a5-215">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="1a4a5-216">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1a4a5-217">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1a4a5-218">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="1a4a5-218">Support authorization</span></span>

<span data-ttu-id="1a4a5-219">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1a4a5-220">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="1a4a5-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="1a4a5-221">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1a4a5-222">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1a4a5-223">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="1a4a5-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1a4a5-224">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1a4a5-225">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1a4a5-226">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-226">The preceding code:</span></span>

* <span data-ttu-id="1a4a5-227">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1a4a5-228">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1a4a5-229">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1a4a5-230">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-230">Update the CreateModel</span></span>

<span data-ttu-id="1a4a5-231">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1a4a5-232">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1a4a5-233">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1a4a5-234">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1a4a5-235">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-235">Update the IndexModel</span></span>

<span data-ttu-id="1a4a5-236">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1a4a5-237">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-237">Update the EditModel</span></span>

<span data-ttu-id="1a4a5-238">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1a4a5-239">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1a4a5-240">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1a4a5-241">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1a4a5-242">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1a4a5-243">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1a4a5-244">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-244">Update the DeleteModel</span></span>

<span data-ttu-id="1a4a5-245">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1a4a5-246">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="1a4a5-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="1a4a5-247">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1a4a5-248">Inserte el servicio de autorización en el archivo *pages/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1a4a5-249">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1a4a5-250">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1a4a5-251">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1a4a5-252">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1a4a5-253">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1a4a5-254">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1a4a5-255">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-255">Update Details</span></span>

<span data-ttu-id="1a4a5-256">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1a4a5-257">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1a4a5-258">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="1a4a5-258">Add or remove a user to a role</span></span>

<span data-ttu-id="1a4a5-259">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1a4a5-260">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-260">Removing privileges from a user.</span></span> <span data-ttu-id="1a4a5-261">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1a4a5-262">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="1a4a5-263">Diferencias entre desafío y prohibido</span><span class="sxs-lookup"><span data-stu-id="1a4a5-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="1a4a5-264">Esta aplicación establece la directiva predeterminada para [requerir usuarios autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="1a4a5-265">El código siguiente permite usuarios anónimos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-265">The following code allows anonymous users.</span></span> <span data-ttu-id="1a4a5-266">Los usuarios anónimos pueden mostrar las diferencias entre desafío y prohibido.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="1a4a5-267">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-267">In the preceding code:</span></span>

* <span data-ttu-id="1a4a5-268">Cuando el usuario **no** está autenticado, `ChallengeResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="1a4a5-269">Cuando `ChallengeResult` se devuelve, se redirige al usuario a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="1a4a5-270">Cuando el usuario está autenticado, pero no autorizado, `ForbidResult` se devuelve un.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="1a4a5-271">Cuando `ForbidResult` se devuelve, se redirige al usuario a la página acceso denegado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1a4a5-272">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="1a4a5-272">Test the completed app</span></span>

<span data-ttu-id="1a4a5-273">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1a4a5-274">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1a4a5-275">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1a4a5-276">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="1a4a5-277">Si la aplicación tiene contactos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-277">If the app has contacts:</span></span>

* <span data-ttu-id="1a4a5-278">Elimine todos los registros de la `Contact` tabla.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="1a4a5-279">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="1a4a5-280">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1a4a5-281">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1a4a5-282">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1a4a5-283">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-283">Verify the following operations:</span></span>

* <span data-ttu-id="1a4a5-284">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1a4a5-285">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1a4a5-286">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1a4a5-287">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1a4a5-288">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1a4a5-289">Usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-289">User</span></span>                | <span data-ttu-id="1a4a5-290">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="1a4a5-290">Seeded by the app</span></span> | <span data-ttu-id="1a4a5-291">Opciones</span><span class="sxs-lookup"><span data-stu-id="1a4a5-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1a4a5-292">No</span><span class="sxs-lookup"><span data-stu-id="1a4a5-292">No</span></span>                | <span data-ttu-id="1a4a5-293">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1a4a5-294">Sí</span><span class="sxs-lookup"><span data-stu-id="1a4a5-294">Yes</span></span>               | <span data-ttu-id="1a4a5-295">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1a4a5-296">Sí</span><span class="sxs-lookup"><span data-stu-id="1a4a5-296">Yes</span></span>               | <span data-ttu-id="1a4a5-297">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1a4a5-298">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1a4a5-299">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1a4a5-300">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1a4a5-301">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="1a4a5-301">Create the starter app</span></span>

* <span data-ttu-id="1a4a5-302">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="1a4a5-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1a4a5-303">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="1a4a5-304">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1a4a5-305">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1a4a5-306">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1a4a5-307">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1a4a5-308">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="1a4a5-309">Si experimenta un error con el `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema de github](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="1a4a5-310">Actualice el delimitador **ContactManager** en el archivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="1a4a5-311">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="1a4a5-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1a4a5-312">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-312">Seed the database</span></span>

<span data-ttu-id="1a4a5-313">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) a la carpeta de *datos* :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="1a4a5-314">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="1a4a5-315">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-315">Test that the app seeded the database.</span></span> <span data-ttu-id="1a4a5-316">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="1a4a5-317">En este tutorial se muestra cómo crear una aplicación Web de ASP.NET Core con los datos de usuario protegidos por autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="1a4a5-318">Muestra una lista de contactos que los usuarios autenticados (registrados) han creado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="1a4a5-319">Hay tres grupos de seguridad:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-319">There are three security groups:</span></span>

* <span data-ttu-id="1a4a5-320">**Los usuarios registrados** pueden ver todos los datos aprobados y pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="1a4a5-321">Los **administradores** pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="1a4a5-322">Los usuarios solo pueden ver los contactos aprobados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="1a4a5-323">**Los administradores** pueden aprobar o rechazar y editar o eliminar los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="1a4a5-324">En la siguiente imagen, el usuario Rick ( `rick@example.com` ) ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="1a4a5-325">Rick solo puede ver contactos aprobados y **Editar** / **eliminar** / **crear nuevos** vínculos para sus contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="1a4a5-326">Solo el último registro, creado por Rick, muestra los vínculos de **edición** y **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="1a4a5-327">Otros usuarios no verán el último registro hasta que un administrador o un administrador cambie el estado a "aprobado".</span><span class="sxs-lookup"><span data-stu-id="1a4a5-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de pantalla que muestra Rick con sesión iniciada](secure-data/_static/rick.png)

<span data-ttu-id="1a4a5-329">En la siguiente imagen, `manager@contoso.com` ha iniciado sesión y en el rol del administrador:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de pantalla que muestra la manager@contoso.com sesión iniciada](secure-data/_static/manager1.png)

<span data-ttu-id="1a4a5-331">La siguiente imagen muestra la vista de detalles de los administradores de un contacto:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-331">The following image shows the managers details view of a contact:</span></span>

![Vista del administrador de un contacto](secure-data/_static/manager.png)

<span data-ttu-id="1a4a5-333">Los botones **aprobar** y **rechazar** solo se muestran para administradores y administradores.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="1a4a5-334">En la siguiente imagen, `admin@contoso.com` ha iniciado sesión y en el rol de administrador:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de pantalla que muestra la admin@contoso.com sesión iniciada](secure-data/_static/admin.png)

<span data-ttu-id="1a4a5-336">El administrador tiene todos los privilegios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-336">The administrator has all privileges.</span></span> <span data-ttu-id="1a4a5-337">Puede leer, modificar o eliminar cualquier contacto y cambiar el estado de los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="1a4a5-338">La aplicación se creó mediante [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) en el siguiente `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="1a4a5-339">El ejemplo contiene los siguientes controladores de autorización:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="1a4a5-340">`ContactIsOwnerAuthorizationHandler`: Garantiza que un usuario solo puede editar sus datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="1a4a5-341">`ContactManagerAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="1a4a5-342">`ContactAdministratorsAuthorizationHandler`: Permite a los administradores aprobar o rechazar contactos y editar o eliminar contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a4a5-343">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-343">Prerequisites</span></span>

<span data-ttu-id="1a4a5-344">Este tutorial es avanzado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-344">This tutorial is advanced.</span></span> <span data-ttu-id="1a4a5-345">Debe estar familiarizado con:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-345">You should be familiar with:</span></span>

* [<span data-ttu-id="1a4a5-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a4a5-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="1a4a5-347">Autenticación</span><span class="sxs-lookup"><span data-stu-id="1a4a5-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1a4a5-348">Confirmación de la cuenta y recuperación de la contraseña</span><span class="sxs-lookup"><span data-stu-id="1a4a5-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="1a4a5-349">Autorización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="1a4a5-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1a4a5-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="1a4a5-351">La aplicación de inicio y completada</span><span class="sxs-lookup"><span data-stu-id="1a4a5-351">The starter and completed app</span></span>

<span data-ttu-id="1a4a5-352">[Descargue](xref:index#how-to-download-a-sample) la aplicación [completada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="1a4a5-353">[Pruebe](#test-the-completed-app) la aplicación completada para familiarizarse con sus características de seguridad.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="1a4a5-354">La aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="1a4a5-354">The starter app</span></span>

<span data-ttu-id="1a4a5-355">[Descargue](xref:index#how-to-download-a-sample) la aplicación de [Inicio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="1a4a5-356">Ejecute la aplicación, pulse el vínculo **ContactManager** y compruebe que puede crear, editar y eliminar un contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="1a4a5-357">Proteger los datos de usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-357">Secure user data</span></span>

<span data-ttu-id="1a4a5-358">En las secciones siguientes se describen todos los pasos principales para crear la aplicación de datos de usuario segura.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="1a4a5-359">Puede que le resulte útil consultar el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="1a4a5-360">Vincular los datos de contacto al usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-360">Tie the contact data to the user</span></span>

<span data-ttu-id="1a4a5-361">Use el [Identity](xref:security/authentication/identity) ID. de usuario de ASP.net para asegurarse de que los usuarios puedan editar sus datos, pero no los datos de otros usuarios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="1a4a5-362">Agregue `OwnerID` y `ContactStatus` al `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="1a4a5-363">`OwnerID` es el identificador del usuario de la `AspNetUser` tabla de la [Identity](xref:security/authentication/identity) base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="1a4a5-364">El `Status` campo determina si los usuarios generales pueden ver un contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="1a4a5-365">Cree una nueva migración y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="1a4a5-366">Agregar servicios de función a Identity</span><span class="sxs-lookup"><span data-stu-id="1a4a5-366">Add Role services to Identity</span></span>

<span data-ttu-id="1a4a5-367">Anexe [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para agregar servicios de rol:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="1a4a5-368">Requerir usuarios autenticados</span><span class="sxs-lookup"><span data-stu-id="1a4a5-368">Require authenticated users</span></span>

<span data-ttu-id="1a4a5-369">Establezca la Directiva de autenticación predeterminada para requerir la autenticación de los usuarios:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="1a4a5-370">Puede rechazar la autenticación en el nivel de la Razor página, el controlador o el método de acción con el `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="1a4a5-371">La configuración de la Directiva de autenticación predeterminada para requerir que los usuarios se autentiquen protege Razor las páginas y controladores recién agregados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="1a4a5-372">Tener autenticación necesaria de forma predeterminada es más seguro que confiar en nuevas páginas y controladores Razor para incluir el `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="1a4a5-373">Agregue [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) a las páginas index, about y Contact para que los usuarios anónimos puedan obtener información sobre el sitio antes de registrarse.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="1a4a5-374">Configurar la cuenta de prueba</span><span class="sxs-lookup"><span data-stu-id="1a4a5-374">Configure the test account</span></span>

<span data-ttu-id="1a4a5-375">La `SeedData` clase crea dos cuentas: administrador y administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="1a4a5-376">Use la [herramienta Administrador de secretos](xref:security/app-secrets) para establecer una contraseña para estas cuentas.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="1a4a5-377">Establezca la contraseña desde el directorio del proyecto (el directorio que contiene *Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="1a4a5-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="1a4a5-378">Si no se especifica una contraseña segura, se produce una excepción cuando `SeedData.Initialize` se llama a.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="1a4a5-379">Actualice `Main` para usar la contraseña de prueba:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="1a4a5-380">Crear las cuentas de prueba y actualizar los contactos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="1a4a5-381">Actualice el `Initialize` método en la `SeedData` clase para crear las cuentas de prueba:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="1a4a5-382">Agregue el identificador de usuario de administrador y `ContactStatus` los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="1a4a5-383">Haga que uno de los contactos "enviado" y otro "rechazado".</span><span class="sxs-lookup"><span data-stu-id="1a4a5-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="1a4a5-384">Agregue el identificador de usuario y el estado a todos los contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="1a4a5-385">Solo se muestra un contacto:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="1a4a5-386">Crear controladores de autorización de propietario, administrador y administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="1a4a5-387">Cree una carpeta de *autorización* y cree una `ContactIsOwnerAuthorizationHandler` clase en ella.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="1a4a5-388">`ContactIsOwnerAuthorizationHandler`Comprueba que el usuario que actúa en un recurso posee el recurso.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="1a4a5-389">El `ContactIsOwnerAuthorizationHandler` contexto de llamadas [. Se realiza correctamente](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) si el usuario autenticado actual es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="1a4a5-390">Los controladores de autorización generalmente:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="1a4a5-391">Devuelve `context.Succeed` cuando se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="1a4a5-392">Devuelve `Task.CompletedTask` cuando no se cumplen los requisitos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="1a4a5-393">`Task.CompletedTask` no es correcto o erróneo &mdash; permite que se ejecuten otros controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="1a4a5-394">Si necesita generar un error explícitamente, devuelva el [contexto. Error](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="1a4a5-395">La aplicación permite que los propietarios de contactos editen o eliminen o creen sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="1a4a5-396">`ContactIsOwnerAuthorizationHandler` no es necesario comprobar la operación que se ha pasado en el parámetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="1a4a5-397">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-397">Create a manager authorization handler</span></span>

<span data-ttu-id="1a4a5-398">Cree una `ContactManagerAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1a4a5-399">`ContactManagerAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="1a4a5-400">Solo los administradores pueden aprobar o rechazar cambios de contenido (nuevos o modificados).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="1a4a5-401">Crear un controlador de autorización de administrador</span><span class="sxs-lookup"><span data-stu-id="1a4a5-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="1a4a5-402">Cree una `ContactAdministratorsAuthorizationHandler` clase en la carpeta de *autorización* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="1a4a5-403">`ContactAdministratorsAuthorizationHandler`Comprueba que el usuario que actúa en el recurso es un administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="1a4a5-404">El administrador puede realizar todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="1a4a5-405">Registrar los controladores de autorización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-405">Register the authorization handlers</span></span>

<span data-ttu-id="1a4a5-406">Los servicios que usan Entity Framework Core deben estar registrados para la [inserción de dependencias](xref:fundamentals/dependency-injection) mediante [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="1a4a5-407">`ContactIsOwnerAuthorizationHandler`Utiliza ASP.net Core [Identity](xref:security/authentication/identity) , que se basa en Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="1a4a5-408">Registre los controladores con la colección de servicios para que estén disponibles para a `ContactsController` través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1a4a5-409">Agregue el código siguiente al final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="1a4a5-410">`ContactAdministratorsAuthorizationHandler` y `ContactManagerAuthorizationHandler` se agregan como singletons.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="1a4a5-411">Son singleton porque no usan EF y toda la información necesaria está en el `Context` parámetro del `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="1a4a5-412">Autorización de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="1a4a5-412">Support authorization</span></span>

<span data-ttu-id="1a4a5-413">En esta sección, actualizará las Razor páginas y agregará una clase de requisitos de operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="1a4a5-414">Revisar la clase de requisitos de las operaciones de contacto</span><span class="sxs-lookup"><span data-stu-id="1a4a5-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="1a4a5-415">Revise la `ContactOperations` clase.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="1a4a5-416">Esta clase contiene los requisitos que admite la aplicación:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="1a4a5-417">Crear una clase base para las páginas de contactos Razor</span><span class="sxs-lookup"><span data-stu-id="1a4a5-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="1a4a5-418">Cree una clase base que contenga los servicios usados en las Razor páginas de contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="1a4a5-419">La clase base coloca el código de inicialización en una ubicación:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="1a4a5-420">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-420">The preceding code:</span></span>

* <span data-ttu-id="1a4a5-421">Agrega el `IAuthorizationService` servicio para tener acceso a los controladores de autorización.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="1a4a5-422">Agrega el Identity `UserManager` servicio.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="1a4a5-423">Agregue la `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="1a4a5-424">Actualización de CreateModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-424">Update the CreateModel</span></span>

<span data-ttu-id="1a4a5-425">Actualice el constructor Create Page Model para usar la `DI_BasePageModel` clase base:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="1a4a5-426">Actualice el `CreateModel.OnPostAsync` método a:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="1a4a5-427">Agregue el identificador de usuario al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="1a4a5-428">Llame al controlador de autorización para comprobar que el usuario tiene permiso para crear contactos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="1a4a5-429">Actualización de IndexModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-429">Update the IndexModel</span></span>

<span data-ttu-id="1a4a5-430">Actualice el `OnGetAsync` método para que solo se muestren los contactos aprobados a los usuarios generales:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="1a4a5-431">Actualización de EditModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-431">Update the EditModel</span></span>

<span data-ttu-id="1a4a5-432">Agregue un controlador de autorización para comprobar que el usuario es el propietario del contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="1a4a5-433">Dado que se está validando la autorización de recursos, el `[Authorize]` atributo no es suficiente.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="1a4a5-434">La aplicación no tiene acceso al recurso cuando se evalúan los atributos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="1a4a5-435">La autorización basada en recursos debe ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="1a4a5-436">Las comprobaciones deben realizarse una vez que la aplicación tenga acceso al recurso, ya sea mediante su carga en el modelo de página o mediante su carga en el propio controlador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="1a4a5-437">Frecuentemente tiene acceso al recurso pasando la clave de recurso.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="1a4a5-438">Actualización de DeleteModel</span><span class="sxs-lookup"><span data-stu-id="1a4a5-438">Update the DeleteModel</span></span>

<span data-ttu-id="1a4a5-439">Actualice el modelo de página de eliminación para que use el controlador de autorización para comprobar que el usuario tiene el permiso eliminar en el contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="1a4a5-440">Inyectar el servicio de autorización en las vistas</span><span class="sxs-lookup"><span data-stu-id="1a4a5-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="1a4a5-441">Actualmente, la interfaz de usuario muestra vínculos de edición y eliminación para contactos que el usuario no puede modificar.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="1a4a5-442">Inserte el servicio de autorización en el archivo *views/_ViewImports. cshtml* para que esté disponible para todas las vistas:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="1a4a5-443">El marcado anterior agrega varias `using` instrucciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="1a4a5-444">Actualice los vínculos de **edición** y **eliminación** en *pages/Contacts/index. cshtml* para que solo se representen para los usuarios con los permisos adecuados:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="1a4a5-445">Ocultar los vínculos de los usuarios que no tienen permiso para cambiar datos no protege la aplicación.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="1a4a5-446">La ocultación de los vínculos hace que la aplicación sea más fácil de ver mostrando solo vínculos válidos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="1a4a5-447">Los usuarios pueden atacar las direcciones URL generadas para invocar operaciones de edición y eliminación en los datos que no son de su propiedad.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="1a4a5-448">La Razor página o el controlador debe aplicar comprobaciones de acceso para proteger los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="1a4a5-449">Detalles de la actualización</span><span class="sxs-lookup"><span data-stu-id="1a4a5-449">Update Details</span></span>

<span data-ttu-id="1a4a5-450">Actualice la vista de detalles para que los administradores puedan aprobar o rechazar contactos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="1a4a5-451">Actualice el modelo de página de detalles:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="1a4a5-452">Agregar o quitar un usuario de un rol</span><span class="sxs-lookup"><span data-stu-id="1a4a5-452">Add or remove a user to a role</span></span>

<span data-ttu-id="1a4a5-453">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obtener información sobre:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="1a4a5-454">Quitar privilegios de un usuario.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-454">Removing privileges from a user.</span></span> <span data-ttu-id="1a4a5-455">Por ejemplo, silenciar a un usuario en una aplicación de chat.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="1a4a5-456">Agregar privilegios a un usuario.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="1a4a5-457">Prueba de la aplicación completada</span><span class="sxs-lookup"><span data-stu-id="1a4a5-457">Test the completed app</span></span>

<span data-ttu-id="1a4a5-458">Si aún no ha establecido una contraseña para las cuentas de usuario inicializadas, use la [herramienta Administrador de secretos](xref:security/app-secrets#secret-manager) para establecer una contraseña:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="1a4a5-459">Elija una contraseña segura: Use ocho o más caracteres y, al menos, un carácter en mayúscula, un número y un símbolo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="1a4a5-460">Por ejemplo, `Passw0rd!` cumple los requisitos de contraseñas seguras.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="1a4a5-461">Ejecute el siguiente comando desde la carpeta del proyecto, donde `<PW>` es la contraseña:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="1a4a5-462">Quitar y actualizar la base de datos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="1a4a5-463">Reinicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="1a4a5-464">Una manera fácil de probar la aplicación completada es iniciar tres exploradores diferentes (o sesiones de incógnito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="1a4a5-465">En un explorador, registre un nuevo usuario (por ejemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="1a4a5-466">Inicie sesión en cada explorador con un usuario diferente.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="1a4a5-467">Compruebe las siguientes operaciones:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-467">Verify the following operations:</span></span>

* <span data-ttu-id="1a4a5-468">Los usuarios registrados pueden ver todos los datos de contacto aprobados.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="1a4a5-469">Los usuarios registrados pueden editar o eliminar sus propios datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="1a4a5-470">Los administradores pueden aprobar o rechazar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="1a4a5-471">La `Details` vista muestra los botones **aprobar** y **rechazar** .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="1a4a5-472">Los administradores pueden aprobar o rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="1a4a5-473">Usuario</span><span class="sxs-lookup"><span data-stu-id="1a4a5-473">User</span></span>                | <span data-ttu-id="1a4a5-474">Inicializado por la aplicación</span><span class="sxs-lookup"><span data-stu-id="1a4a5-474">Seeded by the app</span></span> | <span data-ttu-id="1a4a5-475">Opciones</span><span class="sxs-lookup"><span data-stu-id="1a4a5-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="1a4a5-476">No</span><span class="sxs-lookup"><span data-stu-id="1a4a5-476">No</span></span>                | <span data-ttu-id="1a4a5-477">Edite o elimine los datos propios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="1a4a5-478">Sí</span><span class="sxs-lookup"><span data-stu-id="1a4a5-478">Yes</span></span>               | <span data-ttu-id="1a4a5-479">Aprobar/rechazar y editar o eliminar los datos propios.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="1a4a5-480">Sí</span><span class="sxs-lookup"><span data-stu-id="1a4a5-480">Yes</span></span>               | <span data-ttu-id="1a4a5-481">Aprobar/rechazar y editar o eliminar todos los datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="1a4a5-482">Cree un contacto en el explorador del administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="1a4a5-483">Copie la dirección URL para eliminar y editar del contacto del administrador.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="1a4a5-484">Pegue estos vínculos en el explorador del usuario de prueba para comprobar que el usuario de prueba no puede realizar estas operaciones.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="1a4a5-485">Creación de la aplicación de inicio</span><span class="sxs-lookup"><span data-stu-id="1a4a5-485">Create the starter app</span></span>

* <span data-ttu-id="1a4a5-486">Crear una Razor aplicación de páginas denominada "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="1a4a5-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="1a4a5-487">Cree la aplicación con **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="1a4a5-488">Asígnele el nombre "ContactManager" para que el espacio de nombres coincida con el espacio de nombres usado en el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="1a4a5-489">`-uld` especifica LocalDB en lugar de SQLite.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="1a4a5-490">Agregue *Models/contact. CS*:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="1a4a5-491">Aplicar scaffolding al `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="1a4a5-492">Cree la migración inicial y actualice la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1a4a5-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="1a4a5-493">Actualice el delimitador **ContactManager** en el archivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="1a4a5-494">Prueba de la aplicación mediante la creación, edición y eliminación de un contacto</span><span class="sxs-lookup"><span data-stu-id="1a4a5-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="1a4a5-495">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="1a4a5-495">Seed the database</span></span>

<span data-ttu-id="1a4a5-496">Agregue la clase [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) a la carpeta de *datos* .</span><span class="sxs-lookup"><span data-stu-id="1a4a5-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="1a4a5-497">Llamada `SeedData.Initialize` desde `Main` :</span><span class="sxs-lookup"><span data-stu-id="1a4a5-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="1a4a5-498">Compruebe que la aplicación ha inicializado la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-498">Test that the app seeded the database.</span></span> <span data-ttu-id="1a4a5-499">Si hay alguna fila en la base de de contactos, el método de inicialización no se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="1a4a5-500">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1a4a5-500">Additional resources</span></span>

* [<span data-ttu-id="1a4a5-501">Creación de una aplicación .NET Core y SQL Database en Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1a4a5-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="1a4a5-502">[ASP.net Core laboratorio de autorización](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="1a4a5-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="1a4a5-503">En este laboratorio se incluyen más detalles sobre las características de seguridad que se presentan en este tutorial.</span><span class="sxs-lookup"><span data-stu-id="1a4a5-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="1a4a5-504">Autorización personalizada basada en directivas</span><span class="sxs-lookup"><span data-stu-id="1a4a5-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
