---
title: 'Agregar, descargar y eliminar datos de usuario en Identity un proyecto de ASP.net Core'
author: rick-anderson
description: 'Obtenga información sobre cómo agregar datos de usuario personalizados a Identity en un proyecto de ASP.net Core. Elimine los datos por RGPD.'
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061423"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="e42ed-104">Agregar, descargar y eliminar datos de usuario personalizados Identity en un proyecto de ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e42ed-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="e42ed-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e42ed-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e42ed-106">En este artículo se muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="e42ed-106">This article shows how to:</span></span>

* <span data-ttu-id="e42ed-107">Agregue datos de usuario personalizados a una aplicación Web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e42ed-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="e42ed-108">Marque el modelo de datos de usuario personalizado con el <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributo para que esté disponible automáticamente para su descarga y eliminación.</span><span class="sxs-lookup"><span data-stu-id="e42ed-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="e42ed-109">Hacer que los datos se puedan descargar y eliminar ayuda a cumplir los requisitos de [RGPD](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="e42ed-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="e42ed-110">El ejemplo de proyecto se crea a partir de una Razor aplicación Web de páginas, pero las instrucciones son similares para una aplicación web MVC ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="e42ed-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="e42ed-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e42ed-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e42ed-112">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e42ed-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="e42ed-113">Creación de una Razor aplicación Web</span><span class="sxs-lookup"><span data-stu-id="e42ed-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e42ed-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e42ed-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e42ed-115">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-115">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="e42ed-116">Asigne al proyecto el nombre **WebApp1** si desea que coincida con el espacio de nombres del código de [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="e42ed-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e42ed-117">Seleccionar **ASP.net Core aplicación web** > **correcta**</span><span class="sxs-lookup"><span data-stu-id="e42ed-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e42ed-118">Seleccione **ASP.NET Core 3,0** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="e42ed-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="e42ed-119">Seleccionar **aplicación web** > **correcta**</span><span class="sxs-lookup"><span data-stu-id="e42ed-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e42ed-120">Compile y ejecute el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e42ed-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="e42ed-121">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-121">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="e42ed-122">Asigne al proyecto el nombre **WebApp1** si desea que coincida con el espacio de nombres del código de [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="e42ed-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="e42ed-123">Seleccionar **ASP.net Core aplicación web** > **correcta**</span><span class="sxs-lookup"><span data-stu-id="e42ed-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="e42ed-124">Seleccione **ASP.NET Core 2,2** en la lista desplegable.</span><span class="sxs-lookup"><span data-stu-id="e42ed-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="e42ed-125">Seleccionar **aplicación web** > **correcta**</span><span class="sxs-lookup"><span data-stu-id="e42ed-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="e42ed-126">Compile y ejecute el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e42ed-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="e42ed-127">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e42ed-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="e42ed-128">Ejecutar el Identity scaffolding</span><span class="sxs-lookup"><span data-stu-id="e42ed-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e42ed-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e42ed-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e42ed-130">En **Explorador de soluciones** , haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-130">From **Solution Explorer** , right-click on the project > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="e42ed-131">En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity**  >  **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add** .</span></span>
* <span data-ttu-id="e42ed-132">En el cuadro de diálogo **agregar Identity** , las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="e42ed-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="e42ed-133">Seleccione el archivo de diseño existente  *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e42ed-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="e42ed-134">Seleccione los siguientes archivos para invalidar:</span><span class="sxs-lookup"><span data-stu-id="e42ed-134">Select the following files to override:</span></span>
    * <span data-ttu-id="e42ed-135">**Cuenta/registro**</span><span class="sxs-lookup"><span data-stu-id="e42ed-135">**Account/Register**</span></span>
    * <span data-ttu-id="e42ed-136">**Cuenta/administración/índice**</span><span class="sxs-lookup"><span data-stu-id="e42ed-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="e42ed-137">Seleccione el **+** botón para crear una nueva **clase de contexto de datos** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-137">Select the **+** button to create a new **Data context class** .</span></span> <span data-ttu-id="e42ed-138">Acepte el tipo ( **WebApp1. Models. WebApp1Context** si el proyecto se denomina **WebApp1** ).</span><span class="sxs-lookup"><span data-stu-id="e42ed-138">Accept the type ( **WebApp1.Models.WebApp1Context** if the project is named **WebApp1** ).</span></span>
  * <span data-ttu-id="e42ed-139">Seleccione el **+** botón para crear una nueva **clase de usuario** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-139">Select the **+** button to create a new **User class** .</span></span> <span data-ttu-id="e42ed-140">Acepte el tipo ( **WebApp1User** si el proyecto se denomina **WebApp1** ) > **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-140">Accept the type ( **WebApp1User** if the project is named **WebApp1** ) > **Add** .</span></span>
* <span data-ttu-id="e42ed-141">Seleccione **Agregar** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-141">Select **Add** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e42ed-142">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e42ed-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e42ed-143">Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:</span><span class="sxs-lookup"><span data-stu-id="e42ed-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="e42ed-144">Agregue una referencia de paquete a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al archivo de proyecto (. csproj).</span><span class="sxs-lookup"><span data-stu-id="e42ed-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="e42ed-145">Ejecute el siguiente comando en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="e42ed-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="e42ed-146">Ejecute el siguiente comando para enumerar las Identity Opciones del scaffolding:</span><span class="sxs-lookup"><span data-stu-id="e42ed-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="e42ed-147">En la carpeta del proyecto, ejecute el Identity scaffolding:</span><span class="sxs-lookup"><span data-stu-id="e42ed-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="e42ed-148">Siga las instrucciones de [migraciones, UseAuthentication y diseño](xref:security/authentication/scaffold-identity#efm) para realizar los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e42ed-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="e42ed-149">Cree una migración y actualice la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e42ed-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="e42ed-150">Agregue `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e42ed-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="e42ed-151">Agregue `<partial name="_LoginPartial" />` al archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="e42ed-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="e42ed-152">Pruebe la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e42ed-152">Test the app:</span></span>
  * <span data-ttu-id="e42ed-153">Registrar un usuario</span><span class="sxs-lookup"><span data-stu-id="e42ed-153">Register a user</span></span>
  * <span data-ttu-id="e42ed-154">Seleccione el nuevo nombre de usuario (junto al vínculo de **cierre de sesión** ).</span><span class="sxs-lookup"><span data-stu-id="e42ed-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="e42ed-155">Es posible que necesite expandir la ventana o seleccionar el icono de la barra de navegación para mostrar el nombre de usuario y otros vínculos.</span><span class="sxs-lookup"><span data-stu-id="e42ed-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="e42ed-156">Seleccione la pestaña **datos personales** .</span><span class="sxs-lookup"><span data-stu-id="e42ed-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="e42ed-157">Seleccione el botón **Descargar** y examine el *PersonalData.jsen* el archivo.</span><span class="sxs-lookup"><span data-stu-id="e42ed-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="e42ed-158">Pruebe el botón **eliminar** , que elimina el usuario que ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="e42ed-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="e42ed-159">Agregar datos de usuario personalizados a la base de datos Identity</span><span class="sxs-lookup"><span data-stu-id="e42ed-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="e42ed-160">Actualice la `IdentityUser` clase derivada con propiedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="e42ed-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="e42ed-161">Si ha llamado al proyecto WebApp1, el archivo se denomina *areas/ Identity /Data/WebApp1User.CS* .</span><span class="sxs-lookup"><span data-stu-id="e42ed-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs* .</span></span> <span data-ttu-id="e42ed-162">Actualice el archivo con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e42ed-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="e42ed-163">Las propiedades con el atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) son:</span><span class="sxs-lookup"><span data-stu-id="e42ed-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="e42ed-164">Se elimina cuando la página *areas/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* Razor llama a `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="e42ed-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="e42ed-165">Incluido en los datos descargados en la página *áreas/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="e42ed-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="e42ed-166">Actualización de la página Account/Manage/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="e42ed-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="e42ed-167">Actualice `InputModel` en *areas/ Identity /pages/Account/Manage/index.cshtml.CS* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="e42ed-168">Actualice las *áreas/ Identity /pages/Account/Manage/index.cshtml* con el siguiente marcado resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="e42ed-169">Actualice las *áreas/ Identity /pages/Account/Manage/index.cshtml* con el siguiente marcado resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="e42ed-170">Actualización de la página cuenta/registro. cshtml</span><span class="sxs-lookup"><span data-stu-id="e42ed-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="e42ed-171">Actualice `InputModel` en *areas/ Identity /pages/Account/Register.cshtml.CS* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="e42ed-172">Actualice las *áreas/ Identity /pages/Account/Register.cshtml* con el siguiente marcado resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="e42ed-173">Actualice las *áreas/ Identity /pages/Account/Register.cshtml* con el siguiente marcado resaltado:</span><span class="sxs-lookup"><span data-stu-id="e42ed-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="e42ed-174">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e42ed-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="e42ed-175">Agregar una migración para los datos de usuario personalizados</span><span class="sxs-lookup"><span data-stu-id="e42ed-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e42ed-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e42ed-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e42ed-177">En la consola del **Administrador de paquetes** de Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e42ed-177">In the Visual Studio **Package Manager Console** :</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e42ed-178">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e42ed-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="e42ed-179">Prueba crear, ver, descargar y eliminar datos de usuario personalizados</span><span class="sxs-lookup"><span data-stu-id="e42ed-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="e42ed-180">Pruebe la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e42ed-180">Test the app:</span></span>

* <span data-ttu-id="e42ed-181">Registra un nuevo usuario.</span><span class="sxs-lookup"><span data-stu-id="e42ed-181">Register a new user.</span></span>
* <span data-ttu-id="e42ed-182">Vea los datos de usuario personalizados en la `/Identity/Account/Manage` página.</span><span class="sxs-lookup"><span data-stu-id="e42ed-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="e42ed-183">Descargue y vea los datos personales de los usuarios en la `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="e42ed-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="e42ed-184">Adición de notificaciones al Identity uso de IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="e42ed-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="e42ed-185">Esta sección no es una extensión del tutorial anterior.</span><span class="sxs-lookup"><span data-stu-id="e42ed-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="e42ed-186">Para aplicar los pasos siguientes a la aplicación compilada con el tutorial, consulte [este problema de github](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="e42ed-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="e42ed-187">Se pueden agregar notificaciones adicionales a ASP.NET Core Identity mediante la `IUserClaimsPrincipalFactory<T>` interfaz.</span><span class="sxs-lookup"><span data-stu-id="e42ed-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="e42ed-188">Esta clase se puede Agregar a la aplicación en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="e42ed-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e42ed-189">Agregue la implementación personalizada de la clase como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="e42ed-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="e42ed-190">El código de demostración utiliza la `ApplicationUser` clase.</span><span class="sxs-lookup"><span data-stu-id="e42ed-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="e42ed-191">Esta clase agrega una `IsAdmin` propiedad que se usa para agregar la demanda adicional.</span><span class="sxs-lookup"><span data-stu-id="e42ed-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="e42ed-192">`AdditionalUserClaimsPrincipalFactory` implementa la interfaz `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="e42ed-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="e42ed-193">Se agrega una nueva demanda de rol a `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="e42ed-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="e42ed-194">La demanda adicional se puede usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e42ed-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="e42ed-195">En una Razor página, la `IAuthorizationService` instancia se puede utilizar para tener acceso al valor de la demanda.</span><span class="sxs-lookup"><span data-stu-id="e42ed-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
