---
title: Introducción a Identity en ASP.net Core
author: rick-anderson
description: Se usa Identity con una aplicación ASP.net Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: ad4184fce494ba06acf7e583a42a54d04d37ea20
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556650"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="f6bc4-104">Introducción a Identity en ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6bc4-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6bc4-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f6bc4-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f6bc4-107">Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="f6bc4-108">Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="f6bc4-109">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f6bc4-110">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="f6bc4-111">El [ Identity código fuente](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponible en github.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="f6bc4-112">[Scaffolding Identity ](xref:security/authentication/scaffold-identity) y vea los archivos generados para revisar la interacción de la plantilla con Identity .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="f6bc4-113">Identity normalmente se configura mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f6bc4-114">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f6bc4-115">En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f6bc4-116">Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="f6bc4-117">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte [pasos siguientes](#next).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="f6bc4-118">La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="f6bc4-119">Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="f6bc4-120">No relacionado con ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="f6bc4-121">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f6bc4-122">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="f6bc4-122">Create a Web app with authentication</span></span>

<span data-ttu-id="f6bc4-123">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6bc4-125">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f6bc4-126">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f6bc4-127">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f6bc4-128">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-128">Click **OK**.</span></span>
* <span data-ttu-id="f6bc4-129">Seleccione una **aplicación web** de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f6bc4-130">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-131">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f6bc4-132">El comando anterior crea una Razor aplicación web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f6bc4-133">Para crear la aplicación web con LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f6bc4-134">El proyecto generado proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f6bc4-135">La Identity Razor biblioteca de clases expone extremos con el `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f6bc4-136">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-136">For example:</span></span>

* <span data-ttu-id="f6bc4-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f6bc4-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="f6bc4-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f6bc4-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f6bc4-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f6bc4-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f6bc4-140">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="f6bc4-140">Apply migrations</span></span>

<span data-ttu-id="f6bc4-141">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6bc4-143">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="f6bc4-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-144">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6bc4-145">No es necesario realizar migraciones en este paso al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="f6bc4-146">Para LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f6bc4-147">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="f6bc4-147">Test Register and Login</span></span>

<span data-ttu-id="f6bc4-148">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-148">Run the app and register a user.</span></span> <span data-ttu-id="f6bc4-149">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="f6bc4-150">Configurar Identity servicios</span><span class="sxs-lookup"><span data-stu-id="f6bc4-150">Configure Identity services</span></span>

<span data-ttu-id="f6bc4-151">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f6bc4-152">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="f6bc4-153">El código resaltado anterior configura Identity con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f6bc4-154">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f6bc4-155">Identity se habilita mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f6bc4-156">`UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="f6bc4-157">El código anterior configura Identity con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f6bc4-158">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f6bc4-159">Identity se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f6bc4-160">`UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6bc4-161">La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f6bc4-162">`app.UseAuthorization` se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f6bc4-163">`UseRouting``UseAuthentication` `UseAuthorization` `UseEndpoints` se debe llamar a,, y en el orden mostrado en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f6bc4-164">Para obtener más información sobre `IdentityOptions` y `Startup` , vea <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="f6bc4-165">Scaffold registro, Inicio de sesión, cierre de sesión y RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="f6bc4-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6bc4-167">Agregue los `Register` `Login` archivos,, `LogOut` y `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="f6bc4-168">Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-169">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6bc4-170">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f6bc4-171">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f6bc4-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="f6bc4-172">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f6bc4-173">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f6bc4-174">Para obtener más información sobre scaffolding Identity , vea [scaffolding Identity into a Razor Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f6bc4-175">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="f6bc4-175">Examine Register</span></span>

<span data-ttu-id="f6bc4-176">Cuando un usuario hace clic en el botón **registrar** de la `Register` página, `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f6bc4-177">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="f6bc4-178">Registro</span><span class="sxs-lookup"><span data-stu-id="f6bc4-178">Log in</span></span>

<span data-ttu-id="f6bc4-179">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="f6bc4-180">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f6bc4-181">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f6bc4-182">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f6bc4-183">`PasswordSignInAsync` en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f6bc4-184">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f6bc4-185">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="f6bc4-185">Log out</span></span>

<span data-ttu-id="f6bc4-186">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f6bc4-187">En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f6bc4-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en un cookie .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f6bc4-189">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="f6bc4-190">Muestre Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-190">Test Identity</span></span>

<span data-ttu-id="f6bc4-191">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f6bc4-192">Para probar Identity , agregue [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="f6bc4-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f6bc4-193">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f6bc4-194">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-194">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="f6bc4-195">Visite Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-195">Explore Identity</span></span>

<span data-ttu-id="f6bc4-196">Para explorar Identity con más detalle:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f6bc4-197">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="f6bc4-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f6bc4-198">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="f6bc4-199">Identity Componentes</span><span class="sxs-lookup"><span data-stu-id="f6bc4-199">Identity Components</span></span>

<span data-ttu-id="f6bc4-200">Todos los Identity paquetes de NuGet dependientes de se incluyen en el [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f6bc4-201">El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f6bc4-202">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y lo incluye `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="f6bc4-203">Migrar a ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f6bc4-204">Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f6bc4-205">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="f6bc4-205">Setting password strength</span></span>

<span data-ttu-id="f6bc4-206">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="f6bc4-207">AddDefault Identity y agregarIdentity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f6bc4-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f6bc4-209">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f6bc4-210">Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="f6bc4-211">Impedir la publicación de Identity recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="f6bc4-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="f6bc4-212">Para evitar la publicación de recursos estáticos Identity (hojas de estilos y archivos JavaScript para Identity la interfaz de usuario) en la raíz Web, agregue la siguiente `ResolveStaticWebAssetsInputsDependsOn` propiedad y `RemoveIdentityAssets` destino al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="f6bc4-213">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f6bc4-213">Next Steps</span></span>

* <span data-ttu-id="f6bc4-214">[Código fuente ASP.NET Core Identity](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="f6bc4-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="f6bc4-215">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="f6bc4-216">Configurar Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6bc4-217">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6bc4-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f6bc4-218">ASP.NET Core Identity es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.NET Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f6bc4-219">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en Identity o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f6bc4-220">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f6bc4-221">Identity se puede configurar mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f6bc4-222">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f6bc4-223">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f6bc4-224">En este tema, aprenderá a usar Identity para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f6bc4-225">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan Identity , consulte la sección pasos siguientes al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="f6bc4-226">AddDefault Identity y agregarIdentity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f6bc4-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f6bc4-228">Llamar `AddDefaultIdentity` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f6bc4-229">Vea [ Identity origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f6bc4-230">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="f6bc4-230">Create a Web app with authentication</span></span>

<span data-ttu-id="f6bc4-231">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6bc4-233">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f6bc4-234">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f6bc4-235">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f6bc4-236">Haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-236">Click **OK**.</span></span>
* <span data-ttu-id="f6bc4-237">Seleccione una **aplicación web** de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f6bc4-238">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-239">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f6bc4-240">El proyecto generado proporciona [ASP.NET Core Identity](xref:security/authentication/identity) como una [ Razor biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f6bc4-241">La Identity Razor biblioteca de clases expone extremos con el `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f6bc4-242">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-242">For example:</span></span>

* <span data-ttu-id="f6bc4-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f6bc4-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="f6bc4-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f6bc4-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f6bc4-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f6bc4-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f6bc4-246">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="f6bc4-246">Apply migrations</span></span>

<span data-ttu-id="f6bc4-247">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6bc4-249">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="f6bc4-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-250">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f6bc4-251">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="f6bc4-251">Test Register and Login</span></span>

<span data-ttu-id="f6bc4-252">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-252">Run the app and register a user.</span></span> <span data-ttu-id="f6bc4-253">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="f6bc4-254">Configurar Identity servicios</span><span class="sxs-lookup"><span data-stu-id="f6bc4-254">Configure Identity services</span></span>

<span data-ttu-id="f6bc4-255">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f6bc4-256">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="f6bc4-257">El código anterior configura Identity con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f6bc4-258">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f6bc4-259">Identity se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f6bc4-260">`UseAuthentication` agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f6bc4-261">Para obtener más información, vea la [ Identity clase de opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f6bc4-262">Registro de scaffolding, Inicio de sesión y cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="f6bc4-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f6bc4-263">Siga la [identidad de scaffolding en un Razor proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6bc4-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6bc4-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6bc4-265">Agregue los archivos de registro, Inicio de sesión y cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f6bc4-266">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="f6bc4-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f6bc4-267">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f6bc4-268">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="f6bc4-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f6bc4-269">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f6bc4-270">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f6bc4-271">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="f6bc4-271">Examine Register</span></span>

<span data-ttu-id="f6bc4-272">Cuando un usuario hace clic en el vínculo de **registro** , `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f6bc4-273">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f6bc4-274">Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f6bc4-275">**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f6bc4-276">Registro</span><span class="sxs-lookup"><span data-stu-id="f6bc4-276">Log in</span></span>

<span data-ttu-id="f6bc4-277">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="f6bc4-278">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f6bc4-279">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f6bc4-280">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f6bc4-281">`PasswordSignInAsync` en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-281">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f6bc4-282">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-282">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f6bc4-283">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="f6bc4-283">Log out</span></span>

<span data-ttu-id="f6bc4-284">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f6bc4-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en un cookie .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f6bc4-286">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="f6bc4-287">Muestre Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-287">Test Identity</span></span>

<span data-ttu-id="f6bc4-288">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f6bc4-289">Para probar Identity , agregue [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f6bc4-290">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f6bc4-291">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-291">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="f6bc4-292">Visite Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-292">Explore Identity</span></span>

<span data-ttu-id="f6bc4-293">Para explorar Identity con más detalle:</span><span class="sxs-lookup"><span data-stu-id="f6bc4-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f6bc4-294">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="f6bc4-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f6bc4-295">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="f6bc4-296">Identity Componentes</span><span class="sxs-lookup"><span data-stu-id="f6bc4-296">Identity Components</span></span>

<span data-ttu-id="f6bc4-297">Todos los Identity paquetes de NuGet dependientes se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f6bc4-298">El paquete principal de Identity es [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f6bc4-299">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core Identity y lo incluye `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="f6bc4-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="f6bc4-300">Migrar a ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f6bc4-301">Para obtener más información e instrucciones sobre cómo migrar el Identity almacén existente, vea [migrar Identity la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f6bc4-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f6bc4-302">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="f6bc4-302">Setting password strength</span></span>

<span data-ttu-id="f6bc4-303">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f6bc4-304">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f6bc4-304">Next Steps</span></span>

* <span data-ttu-id="f6bc4-305">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el Identity uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="f6bc4-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="f6bc4-306">Configurar Identity</span><span class="sxs-lookup"><span data-stu-id="f6bc4-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
