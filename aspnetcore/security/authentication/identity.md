---
title: 'Introducción a :::no-loc(Identity)::: en ASP.net Core'
author: rick-anderson
description: 'Se usa :::no-loc(Identity)::: con una aplicación ASP.net Core. Obtenga información sobre cómo establecer los requisitos de contraseña (RequireDigit, RequiredLength, RequiredUniqueChars, etc.).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160311"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="26b47-104">Introducción a :::no-loc(Identity)::: en ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="26b47-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26b47-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="26b47-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="26b47-106">:::no-loc(Identity)::: de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="26b47-106">ASP.NET Core :::no-loc(Identity)::::</span></span>

* <span data-ttu-id="26b47-107">Es una API que admite la funcionalidad de inicio de sesión de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="26b47-108">Administra usuarios, contraseñas, datos de perfil, roles, notificaciones, tokens, confirmación de correo electrónico, etc.</span><span class="sxs-lookup"><span data-stu-id="26b47-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="26b47-109">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en :::no-loc(Identity)::: o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="26b47-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="26b47-110">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="26b47-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="26b47-111">El [ :::no-loc(Identity)::: código fuente](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) está disponible en github.</span><span class="sxs-lookup"><span data-stu-id="26b47-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="26b47-112">[Scaffolding :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) y vea los archivos generados para revisar la interacción de la plantilla con :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="26b47-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="26b47-113">:::no-loc(Identity):::normalmente se configura mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="26b47-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="26b47-114">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="26b47-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="26b47-115">En este tema, aprenderá a usar :::no-loc(Identity)::: para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="26b47-116">Nota: las plantillas tratan el nombre de usuario y el correo electrónico como los mismos para los usuarios.</span><span class="sxs-lookup"><span data-stu-id="26b47-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="26b47-117">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan :::no-loc(Identity)::: , consulte [pasos siguientes](#next).</span><span class="sxs-lookup"><span data-stu-id="26b47-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="26b47-118">La [plataforma de identidad de Microsoft](/azure/active-directory/develop/) es:</span><span class="sxs-lookup"><span data-stu-id="26b47-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="26b47-119">Evolución de la plataforma de desarrollo de Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="26b47-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="26b47-120">No relacionado con ASP.NET Core :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="26b47-120">Unrelated to ASP.NET Core :::no-loc(Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="26b47-121">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="26b47-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="26b47-122">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="26b47-122">Create a Web app with authentication</span></span>

<span data-ttu-id="26b47-123">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="26b47-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b47-125">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26b47-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="26b47-126">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="26b47-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="26b47-127">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="26b47-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="26b47-128">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b47-128">Click **OK**.</span></span>
* <span data-ttu-id="26b47-129">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="26b47-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="26b47-130">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="26b47-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-131">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="26b47-132">El comando anterior crea una :::no-loc(Razor)::: aplicación web con SQLite.</span><span class="sxs-lookup"><span data-stu-id="26b47-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="26b47-133">Para crear la aplicación web con LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="26b47-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="26b47-134">El proyecto generado proporciona [ASP.net Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="26b47-134">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="26b47-135">La :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases expone extremos con el `:::no-loc(Identity):::` área.</span><span class="sxs-lookup"><span data-stu-id="26b47-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="26b47-136">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="26b47-136">For example:</span></span>

* <span data-ttu-id="26b47-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="26b47-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="26b47-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="26b47-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="26b47-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="26b47-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="26b47-140">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="26b47-140">Apply migrations</span></span>

<span data-ttu-id="26b47-141">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="26b47-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26b47-143">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="26b47-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-144">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="26b47-145">No es necesario realizar migraciones en este paso al usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="26b47-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="26b47-146">Para LocalDB, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="26b47-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="26b47-147">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="26b47-147">Test Register and Login</span></span>

<span data-ttu-id="26b47-148">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-148">Run the app and register a user.</span></span> <span data-ttu-id="26b47-149">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="26b47-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="26b47-150">Configurar :::no-loc(Identity)::: servicios</span><span class="sxs-lookup"><span data-stu-id="26b47-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="26b47-151">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="26b47-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="26b47-152">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="26b47-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="26b47-153">El código resaltado anterior configura :::no-loc(Identity)::: con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="26b47-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="26b47-154">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="26b47-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="26b47-155">:::no-loc(Identity):::se habilita mediante una llamada a <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="26b47-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="26b47-156">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="26b47-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="26b47-157">La aplicación generada por la plantilla no utiliza la [autorización](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="26b47-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="26b47-158">`app.UseAuthorization`se incluye para asegurarse de que se agrega en el orden correcto para que la aplicación agregue autorización.</span><span class="sxs-lookup"><span data-stu-id="26b47-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="26b47-159">`UseRouting``UseAuthentication` `UseAuthorization` `UseEndpoints` se debe llamar a,, y en el orden mostrado en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="26b47-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="26b47-160">Para obtener más información sobre `:::no-loc(Identity):::Options` y `Startup` , vea <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> e inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="26b47-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="26b47-161">Scaffold registro, Inicio de sesión, cierre de sesión y RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="26b47-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26b47-163">Agregue los `Register` `Login` archivos,, `LogOut` y `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="26b47-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="26b47-164">Siga la [identidad de scaffolding en un :::no-loc(Razor)::: proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="26b47-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-165">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="26b47-166">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="26b47-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="26b47-167">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="26b47-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="26b47-168">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="26b47-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="26b47-169">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="26b47-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="26b47-170">Para obtener más información sobre scaffolding :::no-loc(Identity)::: , vea [scaffolding Identity into a :::no-loc(Razor)::: Project with Authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="26b47-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="26b47-171">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="26b47-171">Examine Register</span></span>

<span data-ttu-id="26b47-172">Cuando un usuario hace clic en el botón **registrar** de la `Register` página, `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="26b47-173">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="26b47-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="26b47-174">Registro</span><span class="sxs-lookup"><span data-stu-id="26b47-174">Log in</span></span>

<span data-ttu-id="26b47-175">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="26b47-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="26b47-176">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="26b47-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="26b47-177">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="26b47-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="26b47-178">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="26b47-179">`PasswordSignInAsync`en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="26b47-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="26b47-180">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="26b47-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="26b47-181">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="26b47-181">Log out</span></span>

<span data-ttu-id="26b47-182">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="26b47-183">En el código anterior, el código `return RedirectToPage();` debe ser un redireccionamiento para que el explorador realice una nueva solicitud y se actualice la identidad del usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="26b47-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="26b47-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="26b47-185">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="26b47-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="26b47-186">Muestre:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-187">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="26b47-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="26b47-188">Para probar :::no-loc(Identity)::: , agregue [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="26b47-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="26b47-189">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="26b47-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="26b47-190">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="26b47-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="26b47-191">Visite:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-192">Para explorar :::no-loc(Identity)::: con más detalle:</span><span class="sxs-lookup"><span data-stu-id="26b47-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="26b47-193">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="26b47-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="26b47-194">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="26b47-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="26b47-195">:::no-loc(Identity):::Componentes</span><span class="sxs-lookup"><span data-stu-id="26b47-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="26b47-196">Todos los :::no-loc(Identity)::: paquetes de NuGet dependientes de se incluyen en el [ASP.net Core marco de trabajo compartido](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="26b47-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="26b47-197">El paquete principal de :::no-loc(Identity)::: es [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="26b47-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="26b47-198">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: y se incluye en `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="26b47-198">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="26b47-199">Migrar a ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-199">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-200">Para obtener más información e instrucciones sobre cómo migrar el :::no-loc(Identity)::: almacén existente, vea [migrar :::no-loc(Identity)::: la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="26b47-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="26b47-201">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="26b47-201">Setting password strength</span></span>

<span data-ttu-id="26b47-202">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="26b47-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="26b47-203">AddDefault :::no-loc(Identity)::: y agregar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="26b47-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="26b47-205">Llamar `AddDefault:::no-loc(Identity):::` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="26b47-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="26b47-206">Vea [ :::no-loc(Identity)::: origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="26b47-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="26b47-207">Impedir la publicación de :::no-loc(Identity)::: recursos estáticos</span><span class="sxs-lookup"><span data-stu-id="26b47-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="26b47-208">Para evitar la publicación de recursos estáticos :::no-loc(Identity)::: (hojas de estilos y archivos JavaScript para :::no-loc(Identity)::: la interfaz de usuario) en la raíz Web, agregue la siguiente `ResolveStaticWebAssetsInputsDependsOn` propiedad y `Remove:::no-loc(Identity):::Assets` destino al archivo de proyecto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="26b47-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="26b47-209">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="26b47-209">Next Steps</span></span>

* <span data-ttu-id="26b47-210">[:::no-loc(Identity):::Código fuente de ASP.net Core](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="26b47-210">[ASP.NET Core :::no-loc(Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="26b47-211">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el :::no-loc(Identity)::: uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="26b47-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="26b47-212">Configurar :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26b47-213">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="26b47-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="26b47-214">ASP.NET Core :::no-loc(Identity)::: es un sistema de pertenencia que agrega funcionalidad de inicio de sesión a ASP.net Core aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="26b47-214">ASP.NET Core :::no-loc(Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="26b47-215">Los usuarios pueden crear una cuenta con la información de inicio de sesión almacenada en :::no-loc(Identity)::: o pueden utilizar un proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="26b47-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="26b47-216">Entre los proveedores de inicio de sesión externos admitidos se incluyen [Facebook, Google, cuenta de Microsoft y Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="26b47-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="26b47-217">:::no-loc(Identity):::se puede configurar mediante una base de datos de SQL Server para almacenar nombres de usuario, contraseñas y datos de perfil.</span><span class="sxs-lookup"><span data-stu-id="26b47-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="26b47-218">Como alternativa, se puede usar otro almacén persistente, por ejemplo, Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="26b47-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="26b47-219">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([Cómo descargar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="26b47-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="26b47-220">En este tema, aprenderá a usar :::no-loc(Identity)::: para registrar, iniciar sesión y cerrar la sesión de un usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="26b47-221">Para obtener instrucciones más detalladas sobre la creación de aplicaciones que usan :::no-loc(Identity)::: , consulte la sección pasos siguientes al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="26b47-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="26b47-222">AddDefault :::no-loc(Identity)::: y agregar:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>se presentó en ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="26b47-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="26b47-224">Llamar `AddDefault:::no-loc(Identity):::` a es similar a llamar a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="26b47-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="26b47-225">Vea [ :::no-loc(Identity)::: origen de AddDefault](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="26b47-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="26b47-226">Creación de una aplicación web con autenticación</span><span class="sxs-lookup"><span data-stu-id="26b47-226">Create a Web app with authentication</span></span>

<span data-ttu-id="26b47-227">Cree un proyecto de aplicación Web de ASP.NET Core con cuentas de usuario individuales.</span><span class="sxs-lookup"><span data-stu-id="26b47-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26b47-229">Seleccione **archivo** > **nuevo** > **proyecto**.</span><span class="sxs-lookup"><span data-stu-id="26b47-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="26b47-230">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="26b47-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="26b47-231">Asigne al proyecto el nombre **WebApp1** para que tenga el mismo espacio de nombres que la descarga del proyecto.</span><span class="sxs-lookup"><span data-stu-id="26b47-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="26b47-232">Haga clic en **OK**.</span><span class="sxs-lookup"><span data-stu-id="26b47-232">Click **OK**.</span></span>
* <span data-ttu-id="26b47-233">Seleccione una **aplicación web**de ASP.net Core y, a continuación, seleccione **cambiar autenticación**.</span><span class="sxs-lookup"><span data-stu-id="26b47-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="26b47-234">Seleccione **cuentas de usuario individuales** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="26b47-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-235">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="26b47-236">El proyecto generado proporciona [ASP.net Core :::no-loc(Identity)::: ](xref:security/authentication/identity) como [ :::no-loc(Razor)::: biblioteca de clases](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="26b47-236">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="26b47-237">La :::no-loc(Identity)::: :::no-loc(Razor)::: biblioteca de clases expone extremos con el `:::no-loc(Identity):::` área.</span><span class="sxs-lookup"><span data-stu-id="26b47-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="26b47-238">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="26b47-238">For example:</span></span>

* <span data-ttu-id="26b47-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="26b47-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="26b47-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="26b47-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="26b47-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="26b47-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="26b47-242">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="26b47-242">Apply migrations</span></span>

<span data-ttu-id="26b47-243">Aplique las migraciones para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="26b47-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26b47-245">Ejecute el siguiente comando en la consola del administrador de paquetes (PMC):</span><span class="sxs-lookup"><span data-stu-id="26b47-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-246">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="26b47-247">Probar registro e inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="26b47-247">Test Register and Login</span></span>

<span data-ttu-id="26b47-248">Ejecute la aplicación y registre un usuario.</span><span class="sxs-lookup"><span data-stu-id="26b47-248">Run the app and register a user.</span></span> <span data-ttu-id="26b47-249">En función del tamaño de la pantalla, es posible que tenga que seleccionar el botón de alternancia de navegación para ver los vínculos de **Inicio de sesión** y **registro** .</span><span class="sxs-lookup"><span data-stu-id="26b47-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="26b47-250">Configurar :::no-loc(Identity)::: servicios</span><span class="sxs-lookup"><span data-stu-id="26b47-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="26b47-251">Los servicios se agregan en `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="26b47-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="26b47-252">El patrón habitual consiste en llamar a todos los métodos `Add{Service}` y, luego, a todos los métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="26b47-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="26b47-253">El código anterior configura :::no-loc(Identity)::: con valores de opción predeterminados.</span><span class="sxs-lookup"><span data-stu-id="26b47-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="26b47-254">Los servicios se ponen a disposición de la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="26b47-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="26b47-255">:::no-loc(Identity):::se habilita llamando a [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="26b47-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="26b47-256">`UseAuthentication`agrega [middleware](xref:fundamentals/middleware/index) de autenticación a la canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="26b47-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="26b47-257">Para obtener más información, vea la [ :::no-loc(Identity)::: clase de opciones](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) y el inicio de la [aplicación](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="26b47-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="26b47-258">Registro de scaffolding, Inicio de sesión y cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="26b47-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="26b47-259">Siga la [identidad de scaffolding en un :::no-loc(Razor)::: proyecto con](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instrucciones de autorización para generar el código que se muestra en esta sección.</span><span class="sxs-lookup"><span data-stu-id="26b47-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26b47-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26b47-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="26b47-261">Agregue los archivos de registro, Inicio de sesión y cierre de sesión.</span><span class="sxs-lookup"><span data-stu-id="26b47-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26b47-262">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="26b47-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="26b47-263">Si ha creado el proyecto con el nombre **WebApp1**, ejecute los siguientes comandos.</span><span class="sxs-lookup"><span data-stu-id="26b47-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="26b47-264">De lo contrario, use el espacio de nombres correcto para `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="26b47-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="26b47-265">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="26b47-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="26b47-266">Al usar PowerShell, use el carácter de escape en la lista de archivos o ponga la lista de archivos entre comillas dobles, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="26b47-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="26b47-267">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="26b47-267">Examine Register</span></span>

<span data-ttu-id="26b47-268">Cuando un usuario hace clic en el vínculo de **registro** , `RegisterModel.OnPostAsync` se invoca la acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="26b47-269">El usuario lo crea [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) en el `_userManager` objeto:</span><span class="sxs-lookup"><span data-stu-id="26b47-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="26b47-270">Si el usuario se creó correctamente, el usuario inicia sesión en la llamada a `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="26b47-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="26b47-271">**Nota:** Consulte [confirmación](xref:security/authentication/accconfirm#prevent-login-at-registration) de la cuenta para conocer los pasos para evitar el inicio de sesión inmediato en el registro.</span><span class="sxs-lookup"><span data-stu-id="26b47-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="26b47-272">Registro</span><span class="sxs-lookup"><span data-stu-id="26b47-272">Log in</span></span>

<span data-ttu-id="26b47-273">El formulario de inicio de sesión se muestra cuando:</span><span class="sxs-lookup"><span data-stu-id="26b47-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="26b47-274">Se selecciona el vínculo **iniciar sesión** .</span><span class="sxs-lookup"><span data-stu-id="26b47-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="26b47-275">Un usuario intenta obtener acceso a una página restringida para la que no está autorizado para acceder **o** cuando el sistema no la ha autenticado.</span><span class="sxs-lookup"><span data-stu-id="26b47-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="26b47-276">Cuando se envía el formulario de la página de inicio de sesión, `OnPostAsync` se llama a la acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="26b47-277">`PasswordSignInAsync`en el `_signInManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="26b47-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="26b47-278">Para obtener información acerca de cómo tomar decisiones de autorización, vea <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="26b47-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="26b47-279">Cerrar la sesión</span><span class="sxs-lookup"><span data-stu-id="26b47-279">Log out</span></span>

<span data-ttu-id="26b47-280">El vínculo de **cierre de sesión** invoca la `LogoutModel.OnPost` acción.</span><span class="sxs-lookup"><span data-stu-id="26b47-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="26b47-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) borra las notificaciones del usuario almacenadas en una cookie.</span><span class="sxs-lookup"><span data-stu-id="26b47-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="26b47-282">Post se especifica en *pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="26b47-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="26b47-283">Muestre:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-284">Las plantillas predeterminadas del proyecto web permiten el acceso anónimo a las páginas principales.</span><span class="sxs-lookup"><span data-stu-id="26b47-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="26b47-285">Para probar :::no-loc(Identity)::: , agregue [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="26b47-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="26b47-286">Si ha iniciado sesión, cierre la sesión. Ejecute la aplicación y seleccione el vínculo de **privacidad** .</span><span class="sxs-lookup"><span data-stu-id="26b47-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="26b47-287">Se le redirige a la página de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="26b47-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="26b47-288">Visite:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-289">Para explorar :::no-loc(Identity)::: con más detalle:</span><span class="sxs-lookup"><span data-stu-id="26b47-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="26b47-290">Crear origen de la interfaz de usuario de identidad completa</span><span class="sxs-lookup"><span data-stu-id="26b47-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="26b47-291">Examine el origen de cada página y recorra el depurador.</span><span class="sxs-lookup"><span data-stu-id="26b47-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="26b47-292">:::no-loc(Identity):::Componentes</span><span class="sxs-lookup"><span data-stu-id="26b47-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="26b47-293">Todos los :::no-loc(Identity)::: paquetes de NuGet dependientes se incluyen en el [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="26b47-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="26b47-294">El paquete principal de :::no-loc(Identity)::: es [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="26b47-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="26b47-295">Este paquete contiene el conjunto principal de interfaces para ASP.NET Core :::no-loc(Identity)::: y se incluye en `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="26b47-295">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="26b47-296">Migrar a ASP.NET Core:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-296">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="26b47-297">Para obtener más información e instrucciones sobre cómo migrar el :::no-loc(Identity)::: almacén existente, vea [migrar :::no-loc(Identity)::: la autenticación y ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="26b47-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="26b47-298">Establecer la seguridad de la contraseña</span><span class="sxs-lookup"><span data-stu-id="26b47-298">Setting password strength</span></span>

<span data-ttu-id="26b47-299">Consulte [configuración](#pw) para obtener un ejemplo que establece los requisitos mínimos de contraseña.</span><span class="sxs-lookup"><span data-stu-id="26b47-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="26b47-300">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="26b47-300">Next Steps</span></span>

* <span data-ttu-id="26b47-301">Consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/5131) para obtener información sobre cómo configurar el :::no-loc(Identity)::: uso de SQLite.</span><span class="sxs-lookup"><span data-stu-id="26b47-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="26b47-302">Configurar :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="26b47-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
