---
title: Introducción a ASP.NET Core
author: rick-anderson
description: Tutorial breve que crea y ejecuta una aplicación Hola mundo básica mediante ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
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
uid: getting-started
ms.openlocfilehash: fef5ae525a7c01d0ea7733e990233f413aac61a7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057796"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="e71ce-103">Tutorial: Introducción a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e71ce-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="e71ce-104">En este tutorial se muestra cómo crear y ejecutar una aplicación web ASP.NET Core con la CLI de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e71ce-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="e71ce-105">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="e71ce-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e71ce-106">Crear un proyecto de aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e71ce-106">Create a web app project.</span></span>
> * <span data-ttu-id="e71ce-107">Confíe en el certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e71ce-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="e71ce-108">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e71ce-108">Run the app.</span></span>
> * <span data-ttu-id="e71ce-109">Editar una página de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="e71ce-109">Edit a :::no-loc(Razor)::: page.</span></span>

<span data-ttu-id="e71ce-110">Al final, tendrá una aplicación web en funcionamiento ejecutándose en el equipo local.</span><span class="sxs-lookup"><span data-stu-id="e71ce-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Página principal de aplicación web](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="e71ce-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e71ce-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="e71ce-113">Crear un proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="e71ce-113">Create a web app project</span></span>

<span data-ttu-id="e71ce-114">Abra un shell de comandos y escriba el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="e71ce-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="e71ce-115">El comando anterior:</span><span class="sxs-lookup"><span data-stu-id="e71ce-115">The preceding command:</span></span>

* <span data-ttu-id="e71ce-116">Crea una nueva aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e71ce-116">Creates a new web app.</span></span>  
* <span data-ttu-id="e71ce-117">El parámetro `-o aspnetcoreapp` crea un directorio llamado *aspnetcoreapp* con los archivos de código fuente de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e71ce-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="e71ce-118">Confíe en el certificado de desarrollo</span><span class="sxs-lookup"><span data-stu-id="e71ce-118">Trust the development certificate</span></span>

<span data-ttu-id="e71ce-119">Confíe en el certificado de desarrollo HTTPS:</span><span class="sxs-lookup"><span data-stu-id="e71ce-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="e71ce-120">Windows</span><span class="sxs-lookup"><span data-stu-id="e71ce-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="e71ce-121">El comando anterior muestra el siguiente cuadro de diálogo:</span><span class="sxs-lookup"><span data-stu-id="e71ce-121">The preceding command displays the following dialog:</span></span>

![Cuadro de diálogo de advertencia de seguridad](~/getting-started/_static/cert.png)

<span data-ttu-id="e71ce-123">Si acepta confiar en el certificado de desarrollo, seleccione **Sí** .</span><span class="sxs-lookup"><span data-stu-id="e71ce-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="e71ce-124">macOS</span><span class="sxs-lookup"><span data-stu-id="e71ce-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="e71ce-125">El comando anterior muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="e71ce-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="e71ce-126">*Se ha solicitado que confíe en el certificado de desarrollo HTTPS. Si el certificado todavía no es de confianza, se ejecutará el comando siguiente:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.</span><span class="sxs-lookup"><span data-stu-id="e71ce-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="e71ce-127">Este comando podría solicitarle la contraseña para instalar el certificado en la cadena de claves del sistema.</span><span class="sxs-lookup"><span data-stu-id="e71ce-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="e71ce-128">Si acepta confiar en el certificado de desarrollo, escriba la contraseña.</span><span class="sxs-lookup"><span data-stu-id="e71ce-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="e71ce-129">Linux</span><span class="sxs-lookup"><span data-stu-id="e71ce-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="e71ce-130">Consulte la documentación correspondiente a su distribución Linux sobre cómo confiar en el certificado de desarrollo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e71ce-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="e71ce-131">Para más información, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS ](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="e71ce-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="e71ce-132">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="e71ce-132">Run the app</span></span>

<span data-ttu-id="e71ce-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e71ce-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="e71ce-134">Después de que el shell de comandos indique que se ha iniciado la aplicación, vaya a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e71ce-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-no-locrazor-page"></a><span data-ttu-id="e71ce-135">Edición de una página de :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="e71ce-135">Edit a :::no-loc(Razor)::: page</span></span>

<span data-ttu-id="e71ce-136">Abra *Pages/Index.cshtml* y modifique y guarde la página con el siguiente marcado resaltado:</span><span class="sxs-lookup"><span data-stu-id="e71ce-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="e71ce-137">Vaya a `https://localhost:5001`, actualice la página y confirme que los cambios aparecen reflejados.</span><span class="sxs-lookup"><span data-stu-id="e71ce-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e71ce-138">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="e71ce-138">Next steps</span></span>

<span data-ttu-id="e71ce-139">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="e71ce-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e71ce-140">Crear un proyecto de aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e71ce-140">Create a web app project.</span></span>
> * <span data-ttu-id="e71ce-141">Confíe en el certificado de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="e71ce-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="e71ce-142">Ejecute el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e71ce-142">Run the project.</span></span>
> * <span data-ttu-id="e71ce-143">Realizar un cambio.</span><span class="sxs-lookup"><span data-stu-id="e71ce-143">Make a change.</span></span>

<span data-ttu-id="e71ce-144">Para obtener más información sobre ASP.NET Core, vea la ruta de aprendizaje recomendada en la introducción:</span><span class="sxs-lookup"><span data-stu-id="e71ce-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
