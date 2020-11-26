---
title: Autenticación con Facebook, Google y proveedores externos en ASP.NET Core
author: rick-anderson
description: En este tutorial se muestra cómo crear una aplicación de ASP.NET Core mediante OAuth 2.0 con proveedores de autenticación externos.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: ca5fd8f746e759d1994dde9a2a0d5b5fd6c88d1a
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870456"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="6e5a6-103">Autenticación con Facebook, Google y proveedores externos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e5a6-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="6e5a6-104">Por [Valeriy Novytskyy](https://github.com/01binary) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6e5a6-105">En este tutorial se muestra cómo crear una aplicación de ASP.NET Core que permita a los usuarios iniciar sesión mediante OAuth 2.0 con credenciales de proveedores de autenticación externos.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-105">This tutorial demonstrates how to build an ASP.NET Core app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="6e5a6-106">En las siguientes secciones se tratan los proveedores [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) y [Microsoft](xref:security/authentication/microsoft-logins), y usan el proyecto inicial creado en este artículo.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="6e5a6-107">Hay disponibles otros proveedores en paquetes de terceros como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) y [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="6e5a6-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="6e5a6-108">El hecho de permitir a los usuarios iniciar sesión con sus credenciales:</span><span class="sxs-lookup"><span data-stu-id="6e5a6-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="6e5a6-109">resulta muy práctico para ellos;</span><span class="sxs-lookup"><span data-stu-id="6e5a6-109">Is convenient for the users.</span></span>
* <span data-ttu-id="6e5a6-110">transfiere muchas de las complejidades de administrar el proceso de inicio de sesión a un tercero.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="6e5a6-111">Para ver ejemplos de cómo los inicios de sesión de las redes sociales pueden controlar las conversiones del tráfico y de clientes, vea los casos prácticos de [Facebook](https://www.facebook.com/unsupportedbrowser) y [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="6e5a6-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="6e5a6-112">Crear un proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e5a6-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6e5a6-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e5a6-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6e5a6-114">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-114">Create a new project.</span></span>
* <span data-ttu-id="6e5a6-115">Seleccione **Aplicación web de ASP.NET Core** y **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="6e5a6-116">Proporcione un **Nombre del proyecto** y confirme o cambie la **Ubicación**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="6e5a6-117">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-117">Select **Create**.</span></span>
* <span data-ttu-id="6e5a6-118">Seleccione la versión más reciente de ASP.NET Core en la lista desplegable (**ASP.NET Core {X.Y}** ) y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="6e5a6-119">En **Autenticación**, seleccione **Cambiar** y establezca la autenticación en **Cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="6e5a6-120">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-120">Select **OK**.</span></span>
* <span data-ttu-id="6e5a6-121">En la ventana **Crear una aplicación web ASP.NET Core**, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6e5a6-122">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e5a6-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6e5a6-123">Abra el terminal.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-123">Open the terminal.</span></span>  <span data-ttu-id="6e5a6-124">Para Visual Studio Code puede abrir el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6e5a6-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="6e5a6-125">Cambie los directorios (`cd`) a una carpeta que contenga el proyecto.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="6e5a6-126">En Windows, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="6e5a6-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="6e5a6-127">En macOS y Linux, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="6e5a6-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="6e5a6-128">El comando `dotnet new` crea un proyecto de RazorPages en la carpeta *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="6e5a6-129">`-au Individual` crea el código para la autenticación individual.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="6e5a6-130">`-uld` usa LocalDB, una versión ligera de SQL Server Express para Windows.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="6e5a6-131">Omita `-uld` para usar SQLite.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="6e5a6-132">El comando `code` abre la carpeta *WebApp1* en una nueva instancia de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="6e5a6-133">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="6e5a6-133">Apply migrations</span></span>

* <span data-ttu-id="6e5a6-134">Ejecute la aplicación y seleccione el vínculo **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="6e5a6-135">Escriba el correo electrónico y la contraseña de la cuenta nueva y, luego, seleccione **Registrarse**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="6e5a6-136">Siga estas instrucciones para aplicar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="6e5a6-137">Uso de SecretManager para almacenar los tokens asignados por los proveedores de inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="6e5a6-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="6e5a6-138">Los proveedores de inicio de sesión de las redes sociales asignan los tokens **Id. de aplicación** y **Secreto de la aplicación** durante el proceso de registro.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="6e5a6-139">La nomenclatura puede variar en función del proveedor.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-139">The exact token names vary by provider.</span></span> <span data-ttu-id="6e5a6-140">Estos tokens representan las credenciales que usa la aplicación para acceder a su API.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="6e5a6-141">Los tokens constituyen los "secretos" que se pueden vincular a la configuración de la aplicación con la ayuda de [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="6e5a6-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="6e5a6-142">El Administrador de secretos es una alternativa más segura al almacenamiento de los tokens en un archivo de configuración, como, por ejemplo, *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6e5a6-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6e5a6-143">Secret Manager solo está pensado para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="6e5a6-144">Puede almacenar y proteger sus secretos de producción y pruebas de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="6e5a6-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="6e5a6-145">Siga los pasos descritos en el tema [Ubicación de almacenamiento segura de secretos de la aplicación en el desarrollo de ASP.NET Core](xref:security/app-secrets) para almacenar los tokens asignados por cada uno de los siguientes proveedores de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="6e5a6-146">Configuración de los proveedores de inicio de sesión requeridos por la aplicación</span><span class="sxs-lookup"><span data-stu-id="6e5a6-146">Setup login providers required by your application</span></span>

<span data-ttu-id="6e5a6-147">En los temas siguientes encontrará información para configurar la aplicación a fin de usar los proveedores correspondientes:</span><span class="sxs-lookup"><span data-stu-id="6e5a6-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="6e5a6-148">Instrucciones para [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="6e5a6-149">Instrucciones para [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="6e5a6-150">Instrucciones para [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="6e5a6-151">Instrucciones para [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="6e5a6-152">Instrucciones para [otros proveedores](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="6e5a6-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="6e5a6-153">Establecimiento opcional de contraseña</span><span class="sxs-lookup"><span data-stu-id="6e5a6-153">Optionally set password</span></span>

<span data-ttu-id="6e5a6-154">Si el registro se realiza mediante un proveedor de inicio de sesión externo, no se tiene ninguna contraseña en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="6e5a6-155">De esta forma no hace falta crear y recordar una contraseña para el sitio, aunque le hace depender del proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="6e5a6-156">Si el proveedor de inicio de sesión externo no está disponible, no podrá iniciar sesión en el sitio web.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="6e5a6-157">Para crear una contraseña e iniciar sesión con el correo electrónico establecido durante el proceso de inicio de sesión con proveedores externos:</span><span class="sxs-lookup"><span data-stu-id="6e5a6-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="6e5a6-158">Seleccione el vínculo **Hola, &lt;alias de correo electrónico&gt;** situado en la esquina superior derecha para ir a la vista **Administración**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Vista Administración de la aplicación web](index/_static/pass1a.png)

* <span data-ttu-id="6e5a6-160">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-160">Select **Create**</span></span>

![Página para establecer la contraseña](index/_static/pass2a.png)

* <span data-ttu-id="6e5a6-162">Establezca una contraseña válida. Podrá usarla para iniciar sesión con su correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6e5a6-163">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="6e5a6-163">Next steps</span></span>

* <span data-ttu-id="6e5a6-164">Consulte [esta incidencia de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/10563) para obtener información sobre cómo personalizar los botones de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="6e5a6-165">En este artículo se introdujo la autenticación externa y se explicaron los requisitos previos necesarios para agregar inicios de sesión externos a la aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="6e5a6-166">Páginas de referencia específicas del proveedor para configurar los inicios de sesión para los proveedores requeridos por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="6e5a6-167">Le recomendamos que conserve los datos adicionales sobre el usuario y sus tokens de acceso y actualización.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="6e5a6-168">Para obtener más información, vea <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="6e5a6-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
