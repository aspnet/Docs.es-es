---
title: Introducción a Identity en ASP.net Core
author: rick-anderson
description: Se usa Identity con una aplicación ASP.net Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445441"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="eb5bc-104">Introducción a Identity en ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb5bc-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb5bc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eb5bc-106">Identity de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="eb5bc-107">Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="eb5bc-108">Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="eb5bc-109">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="eb5bc-110">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="eb5bc-111">El [ Identity código fuente](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponible en github.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="eb5bc-112">[Scaffolding Identity ](xref:security/authentication/scaffold-identity) y vea los archivos generados para revisar la interacción de la plantilla con Identity .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="eb5bc-113">normalmente se configura mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="eb5bc-114">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="eb5bc-115">En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="eb5bc-116">Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="eb5bc-117">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte [pasos siguientes](#next).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="eb5bc-118">La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="eb5bc-119">Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="eb5bc-120">No relacionado con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="eb5bc-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="eb5bc-121">Server4.md)]</span></span>

<span data-ttu-id="eb5bc-122">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="eb5bc-123">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="eb5bc-123">Create a Web app with authentication</span></span>

<span data-ttu-id="eb5bc-124">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eb5bc-126">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="eb5bc-127">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="eb5bc-128">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="eb5bc-129">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-129">Click **OK**.</span></span>
* <span data-ttu-id="eb5bc-130">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="eb5bc-131">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-132">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="eb5bc-133">El comando anterior crea una Razor aplicación web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="eb5bc-134">Para crear la aplicación web con LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="eb5bc-135">El proyecto generado proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="eb5bc-136">La Identity Razor biblioteca de clases expone extremos con el `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="eb5bc-137">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-137">For example:</span></span>

* <span data-ttu-id="eb5bc-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="eb5bc-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="eb5bc-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="eb5bc-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="eb5bc-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="eb5bc-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="eb5bc-141">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="eb5bc-141">Apply migrations</span></span>

<span data-ttu-id="eb5bc-142">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb5bc-144">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="eb5bc-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-145">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb5bc-146">No es necesario realizar migraciones en este paso al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="eb5bc-147">Para LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="eb5bc-148">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="eb5bc-148">Test Register and Login</span></span>

<span data-ttu-id="eb5bc-149">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-149">Run the app and register a user.</span></span> <span data-ttu-id="eb5bc-150">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="eb5bc-151">Configurar Identity servicios</span><span class="sxs-lookup"><span data-stu-id="eb5bc-151">Configure Identity services</span></span>

<span data-ttu-id="eb5bc-152">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="eb5bc-153">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="eb5bc-154">El código resaltado anterior configura Identity con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="eb5bc-155">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="eb5bc-156">se habilita mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="eb5bc-157">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="eb5bc-158">La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="eb5bc-159">`app.UseAuthorization`se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="eb5bc-160">`UseRouting``UseAuthentication` `UseAuthorization` `UseEndpoints` se debe llamar a,, y en el orden mostrado en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="eb5bc-161">Para obtener más información sobre `IdentityOptions` y `Startup` , vea <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="eb5bc-162">Scaffold registro, Inicio de sesión, cierre de sesión y RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="eb5bc-162">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb5bc-164">Agregue los `Register` `Login` archivos,, `LogOut` y `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-164">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="eb5bc-165">Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-166">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb5bc-167">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="eb5bc-168">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="eb5bc-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="eb5bc-169">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="eb5bc-170">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="eb5bc-171">Para obtener más información sobre scaffolding Identity , vea [scaffolding Identity into a Razor Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="eb5bc-172">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="eb5bc-172">Examine Register</span></span>

<span data-ttu-id="eb5bc-173">Cuando un usuario hace clic en el botón **registrar** de la `Register` página, `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-173">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="eb5bc-174">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="eb5bc-175">Registro</span><span class="sxs-lookup"><span data-stu-id="eb5bc-175">Log in</span></span>

<span data-ttu-id="eb5bc-176">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-176">The Login form is displayed when:</span></span>

* <span data-ttu-id="eb5bc-177">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-177">The **Log in** link is selected.</span></span>
* <span data-ttu-id="eb5bc-178">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-178">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="eb5bc-179">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-179">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="eb5bc-180">`PasswordSignInAsync`en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-180">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="eb5bc-181">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-181">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="eb5bc-182">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="eb5bc-182">Log out</span></span>

<span data-ttu-id="eb5bc-183">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-183">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="eb5bc-184">En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-184">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="eb5bc-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="eb5bc-186">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-186">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="eb5bc-187">MuestreIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-187">Test Identity</span></span>

<span data-ttu-id="eb5bc-188">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-188">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="eb5bc-189">Para probar Identity , agregue [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="eb5bc-189">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="eb5bc-190">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-190">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="eb5bc-191">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-191">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="eb5bc-192">VisiteIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-192">Explore Identity</span></span>

<span data-ttu-id="eb5bc-193">Para explorar Identity con más detalle:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-193">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="eb5bc-194">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="eb5bc-194">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="eb5bc-195">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-195">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="eb5bc-196">Componentes</span><span class="sxs-lookup"><span data-stu-id="eb5bc-196"> Components</span></span>

<span data-ttu-id="eb5bc-197">Todos los Identity paquetes de NuGet dependientes de se incluyen en el [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-197">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="eb5bc-198">El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-198">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="eb5bc-199">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y se incluye en `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-199">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="eb5bc-200">Migrar a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="eb5bc-201">Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-201">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="eb5bc-202">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="eb5bc-202">Setting password strength</span></span>

<span data-ttu-id="eb5bc-203">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-203">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="eb5bc-204">AddDefault Identity y agregarIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-204">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="eb5bc-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="eb5bc-206">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-206">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="eb5bc-207">Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-207">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="eb5bc-208">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="eb5bc-208">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="eb5bc-209">Para evitar la publicación de recursos estáticos Identity (hojas de estilos y archivos JavaScript para Identity la interfaz de usuario) en la raíz Web, agregue la siguiente `ResolveStaticWebAssetsInputsDependsOn` propiedad y `RemoveIdentityAssets` destino al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-209">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="eb5bc-210">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="eb5bc-210">Next Steps</span></span>

* <span data-ttu-id="eb5bc-211">[IdentityCódigo fuente de ASP.net Core](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="eb5bc-211">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="eb5bc-212">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="eb5bc-213">[Configurar Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="eb5bc-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb5bc-214">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb5bc-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="eb5bc-215">ASP.NET Core Identity es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.net Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="eb5bc-216">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="eb5bc-217">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="eb5bc-218">se puede configurar mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="eb5bc-219">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="eb5bc-220">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="eb5bc-221">En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="eb5bc-222">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte la sección pasos siguientes al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="eb5bc-223">AddDefault Identity y agregarIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="eb5bc-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="eb5bc-225">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="eb5bc-226">Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="eb5bc-227">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="eb5bc-227">Create a Web app with authentication</span></span>

<span data-ttu-id="eb5bc-228">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eb5bc-230">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="eb5bc-231">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="eb5bc-232">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="eb5bc-233">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-233">Click **OK**.</span></span>
* <span data-ttu-id="eb5bc-234">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="eb5bc-235">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-236">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="eb5bc-237">El proyecto generado proporciona [ASP.net Core Identity ](xref:security/authentication/identity) como [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="eb5bc-238">La Identity Razor biblioteca de clases expone extremos con el `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="eb5bc-239">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-239">For example:</span></span>

* <span data-ttu-id="eb5bc-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="eb5bc-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="eb5bc-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="eb5bc-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="eb5bc-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="eb5bc-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="eb5bc-243">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="eb5bc-243">Apply migrations</span></span>

<span data-ttu-id="eb5bc-244">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb5bc-246">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="eb5bc-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-247">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="eb5bc-248">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="eb5bc-248">Test Register and Login</span></span>

<span data-ttu-id="eb5bc-249">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-249">Run the app and register a user.</span></span> <span data-ttu-id="eb5bc-250">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="eb5bc-251">Configurar Identity servicios</span><span class="sxs-lookup"><span data-stu-id="eb5bc-251">Configure Identity services</span></span>

<span data-ttu-id="eb5bc-252">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="eb5bc-253">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="eb5bc-254">El código anterior configura Identity con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="eb5bc-255">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="eb5bc-256">se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="eb5bc-257">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="eb5bc-258">Para obtener más información, vea la [ Identity clase de opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="eb5bc-259">Registro de scaffolding, Inicio de sesión y cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="eb5bc-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="eb5bc-260">Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb5bc-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb5bc-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb5bc-262">Agregue los archivos de registro, Inicio de sesión y cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eb5bc-263">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eb5bc-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eb5bc-264">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="eb5bc-265">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="eb5bc-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="eb5bc-266">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="eb5bc-267">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="eb5bc-268">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="eb5bc-268">Examine Register</span></span>

<span data-ttu-id="eb5bc-269">Cuando un usuario hace clic en el vínculo de **registro** , `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="eb5bc-270">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="eb5bc-271">Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="eb5bc-272">**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="eb5bc-273">Registro</span><span class="sxs-lookup"><span data-stu-id="eb5bc-273">Log in</span></span>

<span data-ttu-id="eb5bc-274">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="eb5bc-275">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="eb5bc-276">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="eb5bc-277">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="eb5bc-278">`PasswordSignInAsync`en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="eb5bc-279">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="eb5bc-280">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="eb5bc-280">Log out</span></span>

<span data-ttu-id="eb5bc-281">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="eb5bc-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="eb5bc-283">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="eb5bc-284">MuestreIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-284">Test Identity</span></span>

<span data-ttu-id="eb5bc-285">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="eb5bc-286">Para probar Identity , agregue [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="eb5bc-287">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="eb5bc-288">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="eb5bc-289">VisiteIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-289">Explore Identity</span></span>

<span data-ttu-id="eb5bc-290">Para explorar Identity con más detalle:</span><span class="sxs-lookup"><span data-stu-id="eb5bc-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="eb5bc-291">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="eb5bc-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="eb5bc-292">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="eb5bc-293">Componentes</span><span class="sxs-lookup"><span data-stu-id="eb5bc-293"> Components</span></span>

<span data-ttu-id="eb5bc-294">Todos los Identity paquetes de NuGet dependientes se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="eb5bc-295">El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="eb5bc-296">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y se incluye en `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="eb5bc-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="eb5bc-297">Migrar a ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="eb5bc-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="eb5bc-298">Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="eb5bc-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="eb5bc-299">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="eb5bc-299">Setting password strength</span></span>

<span data-ttu-id="eb5bc-300">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eb5bc-301">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="eb5bc-301">Next Steps</span></span>

* <span data-ttu-id="eb5bc-302">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="eb5bc-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="eb5bc-303">[Configurar Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="eb5bc-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
