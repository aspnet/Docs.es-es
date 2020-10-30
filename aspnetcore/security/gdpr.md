---
title: Compatibilidad de Reglamento general de protección de datos (RGPD) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo acceder a los puntos de extensión de RGPD en una aplicación Web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051010"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="c06a1-103">Compatibilidad con Reglamento general de protección de datos de la Unión Europea (RGPD) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c06a1-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="c06a1-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c06a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c06a1-105">ASP.NET Core proporciona API y plantillas para ayudar a cumplir algunos de los requisitos de la Reglamento general de protección de datos de la [UE (RGPD)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="c06a1-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c06a1-106">Las plantillas de proyecto incluyen puntos de extensión y marcado stub que se pueden reemplazar con la Directiva de privacidad y :::no-loc(cookie)::: uso.</span><span class="sxs-lookup"><span data-stu-id="c06a1-106">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="c06a1-107">La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="c06a1-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="c06a1-108">Para habilitar la característica de consentimiento predeterminado, :::no-loc(cookie)::: como la que se encuentra en las plantillas de ASP.NET Core 2,2 en una aplicación de ASP.NET Core 3,0 plantillas generadas:</span><span class="sxs-lookup"><span data-stu-id="c06a1-108">To enable the default :::no-loc(cookie)::: consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="c06a1-109">Agregue `using Microsoft.AspNetCore.Http` a la lista de directivas using.</span><span class="sxs-lookup"><span data-stu-id="c06a1-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="c06a1-110">Agregue [ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) a `Startup.ConfigureServices` y [use la :::no-loc(Cookie)::: Directiva](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) para `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c06a1-110">Add [:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) to `Startup.ConfigureServices` and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="c06a1-111">Agregue el :::no-loc(cookie)::: consentimiento parcial al archivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c06a1-111">Add the :::no-loc(cookie)::: consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="c06a1-112">Agregue el archivo *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* al proyecto:</span><span class="sxs-lookup"><span data-stu-id="c06a1-112">Add the *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

* <span data-ttu-id="c06a1-113">Seleccione la versión ASP.NET Core 2,2 de este artículo para obtener información acerca de la :::no-loc(cookie)::: característica de consentimiento.</span><span class="sxs-lookup"><span data-stu-id="c06a1-113">Select the ASP.NET Core 2.2 version of this article to read about the :::no-loc(cookie)::: consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="c06a1-114">Las plantillas de proyecto incluyen puntos de extensión y marcado stub que se pueden reemplazar con la Directiva de privacidad y :::no-loc(cookie)::: uso.</span><span class="sxs-lookup"><span data-stu-id="c06a1-114">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="c06a1-115">Una :::no-loc(cookie)::: característica de consentimiento le permite solicitar (y realizar un seguimiento) el consentimiento de los usuarios para almacenar información personal.</span><span class="sxs-lookup"><span data-stu-id="c06a1-115">A :::no-loc(cookie)::: consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="c06a1-116">Si un usuario no ha dado su consentimiento a la recopilación de datos y la aplicación tiene [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) establecido en, no se `true` :::no-loc(cookie)::: enviarán s no esenciales al explorador.</span><span class="sxs-lookup"><span data-stu-id="c06a1-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) set to `true`, non-essential :::no-loc(cookie):::s aren't sent to the browser.</span></span>
* <span data-ttu-id="c06a1-117">:::no-loc(Cookie):::s se pueden marcar como esenciales.</span><span class="sxs-lookup"><span data-stu-id="c06a1-117">:::no-loc(Cookie):::s can be marked as essential.</span></span> <span data-ttu-id="c06a1-118">Los :::no-loc(cookie)::: s esenciales se envían al explorador incluso cuando el usuario no ha dado su consentimiento y el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="c06a1-118">Essential :::no-loc(cookie):::s are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="c06a1-119">[TempData y las :::no-loc(cookie)::: sesiones](#tempdata) no funcionan cuando el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="c06a1-119">[TempData and Session :::no-loc(cookie):::s](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="c06a1-120">La página [ :::no-loc(Identity)::: administrar](#pd) proporciona un vínculo para descargar y eliminar los datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="c06a1-120">The [:::no-loc(Identity)::: manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="c06a1-121">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite probar la mayoría de los puntos de extensión RGPD y las API agregadas a las plantillas ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="c06a1-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="c06a1-122">Vea el archivo [Léame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obtener instrucciones de prueba.</span><span class="sxs-lookup"><span data-stu-id="c06a1-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="c06a1-123">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c06a1-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="c06a1-124">Compatibilidad de ASP.NET Core RGPD en el código generado por plantillas</span><span class="sxs-lookup"><span data-stu-id="c06a1-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="c06a1-125">:::no-loc(Razor)::: Las páginas y los proyectos de MVC creados con las plantillas de proyecto incluyen la siguiente compatibilidad con RGPD:</span><span class="sxs-lookup"><span data-stu-id="c06a1-125">:::no-loc(Razor)::: Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="c06a1-126">[ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) y [la :::no-loc(Cookie)::: Directiva de uso](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) se establecen en la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="c06a1-126">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="c06a1-127">[Vista parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)de *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c06a1-127">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="c06a1-128">En este archivo se incluye un botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="c06a1-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="c06a1-129">Cuando el usuario hace clic en el botón **Aceptar** , se proporciona el consentimiento a la tienda :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="c06a1-129">When the user clicks the **Accept** button, consent to store :::no-loc(cookie):::s is provided.</span></span>
* <span data-ttu-id="c06a1-130">La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="c06a1-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="c06a1-131">El archivo *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* genera un vínculo a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="c06a1-131">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="c06a1-132">En el caso de las aplicaciones creadas con cuentas de usuario individuales, la página Administrar proporciona vínculos para descargar y eliminar los [datos personales](#pd)de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="c06a1-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="c06a1-133">:::no-loc(Cookie):::PolicyOptions y uso de la :::no-loc(Cookie)::: Directiva</span><span class="sxs-lookup"><span data-stu-id="c06a1-133">:::no-loc(Cookie):::PolicyOptions and Use:::no-loc(Cookie):::Policy</span></span>

<span data-ttu-id="c06a1-134">[ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) se inicializan en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c06a1-134">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="c06a1-135">[Usar :::no-loc(Cookie)::: ](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) Se llama a la Directiva en `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="c06a1-135">[Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="c06a1-136">\_:::no-loc(Cookie):::Vista parcial de ConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="c06a1-136">\_:::no-loc(Cookie):::ConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="c06a1-137">La vista parcial de *\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c06a1-137">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

<span data-ttu-id="c06a1-138">Este:</span><span class="sxs-lookup"><span data-stu-id="c06a1-138">This partial:</span></span>

* <span data-ttu-id="c06a1-139">Obtiene el estado de seguimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="c06a1-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="c06a1-140">Si la aplicación está configurada para requerir el consentimiento, el usuario debe dar su consentimiento antes de :::no-loc(cookie)::: que se pueda realizar el seguimiento de.</span><span class="sxs-lookup"><span data-stu-id="c06a1-140">If the app is configured to require consent, the user must consent before :::no-loc(cookie):::s can be tracked.</span></span> <span data-ttu-id="c06a1-141">Si se requiere el consentimiento, el :::no-loc(cookie)::: Panel de consentimiento se fija en la parte superior de la barra de navegación creada por el archivo *\_ layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c06a1-141">If consent is required, the :::no-loc(cookie)::: consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="c06a1-142">Proporciona un `<p>` elemento HTML para resumir la privacidad y la :::no-loc(cookie)::: Directiva de uso.</span><span class="sxs-lookup"><span data-stu-id="c06a1-142">Provides an HTML `<p>` element to summarize your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="c06a1-143">Proporciona un vínculo a la página de privacidad o vista donde puede obtener detalles de la Directiva de privacidad del sitio.</span><span class="sxs-lookup"><span data-stu-id="c06a1-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="c06a1-144">Essential :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="c06a1-144">Essential :::no-loc(cookie):::s</span></span>

<span data-ttu-id="c06a1-145">Si no :::no-loc(cookie)::: se ha proporcionado el consentimiento para la tienda, solo :::no-loc(cookie)::: se envían al explorador los s marcados como esenciales.</span><span class="sxs-lookup"><span data-stu-id="c06a1-145">If consent to store :::no-loc(cookie):::s hasn't been provided, only :::no-loc(cookie):::s marked essential are sent to the browser.</span></span> <span data-ttu-id="c06a1-146">El código siguiente hace que sea :::no-loc(cookie)::: esencial:</span><span class="sxs-lookup"><span data-stu-id="c06a1-146">The following code makes a :::no-loc(cookie)::: essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/:::no-loc(Cookie):::.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="c06a1-147">El proveedor TempData y el estado de sesión :::no-loc(cookie)::: s no son esenciales</span><span class="sxs-lookup"><span data-stu-id="c06a1-147">TempData provider and session state :::no-loc(cookie):::s aren't essential</span></span>

<span data-ttu-id="c06a1-148">El [proveedor TempData](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: no es esencial.</span><span class="sxs-lookup"><span data-stu-id="c06a1-148">The [TempData provider](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: isn't essential.</span></span> <span data-ttu-id="c06a1-149">Si el seguimiento está deshabilitado, el proveedor TempData no funciona.</span><span class="sxs-lookup"><span data-stu-id="c06a1-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="c06a1-150">Para habilitar el proveedor TempData cuando el seguimiento está deshabilitado, marque TempData :::no-loc(cookie)::: como Essential en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c06a1-150">To enable the TempData provider when tracking is disabled, mark the TempData :::no-loc(cookie)::: as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="c06a1-151">[Estado](xref:fundamentals/app-state) :::no-loc(cookie)::: de sesión s no son esenciales.</span><span class="sxs-lookup"><span data-stu-id="c06a1-151">[Session state](xref:fundamentals/app-state) :::no-loc(cookie):::s are not essential.</span></span> <span data-ttu-id="c06a1-152">El estado de sesión no funciona cuando el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="c06a1-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="c06a1-153">El código siguiente hace que las sesiones sean :::no-loc(cookie)::: esenciales:</span><span class="sxs-lookup"><span data-stu-id="c06a1-153">The following code makes session :::no-loc(cookie):::s essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="c06a1-154">Datos personales</span><span class="sxs-lookup"><span data-stu-id="c06a1-154">Personal data</span></span>

<span data-ttu-id="c06a1-155">ASP.NET Core aplicaciones creadas con cuentas de usuario individuales incluyen código para descargar y eliminar los datos personales.</span><span class="sxs-lookup"><span data-stu-id="c06a1-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="c06a1-156">Seleccione el nombre de usuario y, a continuación, seleccione **datos personales** :</span><span class="sxs-lookup"><span data-stu-id="c06a1-156">Select the user name and then select **Personal data** :</span></span>

![Página administrar datos personales](gdpr/_static/pd.png)

<span data-ttu-id="c06a1-158">Notas:</span><span class="sxs-lookup"><span data-stu-id="c06a1-158">Notes:</span></span>

* <span data-ttu-id="c06a1-159">Para generar el `Account/Manage` código, consulte [scaffold :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="c06a1-159">To generate the `Account/Manage` code, see [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="c06a1-160">Los vínculos de **eliminación** y **descarga** solo actúan sobre los datos de identidad predeterminados.</span><span class="sxs-lookup"><span data-stu-id="c06a1-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="c06a1-161">Las aplicaciones que crean datos de usuario personalizados deben extenderse para eliminar o descargar los datos de usuario personalizados.</span><span class="sxs-lookup"><span data-stu-id="c06a1-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="c06a1-162">Para obtener más información, vea [Agregar, descargar y eliminar datos de usuario personalizados :::no-loc(Identity)::: en ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="c06a1-162">For more information, see [Add, download, and delete custom user data to :::no-loc(Identity):::](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="c06a1-163">Los tokens guardados para el usuario que se almacenan en la :::no-loc(Identity)::: tabla de base de datos `AspNetUserTokens` se eliminan cuando se elimina el usuario mediante el comportamiento de eliminación en cascada debido a la [clave externa](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="c06a1-163">Saved tokens for the user that are stored in the :::no-loc(Identity)::: database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span></span>
* <span data-ttu-id="c06a1-164">La [autenticación de proveedor externo](xref:security/authentication/social/index), como Facebook y Google, no está disponible antes de que :::no-loc(cookie)::: se acepte la Directiva.</span><span class="sxs-lookup"><span data-stu-id="c06a1-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the :::no-loc(cookie)::: policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="c06a1-165">Cifrado en reposo</span><span class="sxs-lookup"><span data-stu-id="c06a1-165">Encryption at rest</span></span>

<span data-ttu-id="c06a1-166">Algunas bases de datos y mecanismos de almacenamiento permiten el cifrado en reposo.</span><span class="sxs-lookup"><span data-stu-id="c06a1-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="c06a1-167">Cifrado en reposo:</span><span class="sxs-lookup"><span data-stu-id="c06a1-167">Encryption at rest:</span></span>

* <span data-ttu-id="c06a1-168">Cifra automáticamente los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="c06a1-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="c06a1-169">Cifra sin configuración, programación u otro trabajo para el software que tiene acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="c06a1-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="c06a1-170">Es la opción más sencilla y más segura.</span><span class="sxs-lookup"><span data-stu-id="c06a1-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="c06a1-171">Permite a la base de datos administrar las claves y el cifrado.</span><span class="sxs-lookup"><span data-stu-id="c06a1-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="c06a1-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c06a1-172">For example:</span></span>

* <span data-ttu-id="c06a1-173">Microsoft SQL y Azure SQL proporcionan [cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="c06a1-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="c06a1-174">SQL Azure cifra la base de datos de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="c06a1-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="c06a1-175">[Los blobs de Azure, los archivos, las tablas y los Queue Storage están cifrados de forma predeterminada](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="c06a1-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="c06a1-176">En el caso de las bases de datos que no proporcionan cifrado integrado en reposo, es posible que pueda usar el cifrado de disco para proporcionar la misma protección.</span><span class="sxs-lookup"><span data-stu-id="c06a1-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="c06a1-177">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c06a1-177">For example:</span></span>

* [<span data-ttu-id="c06a1-178">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="c06a1-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="c06a1-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="c06a1-179">Linux:</span></span>
  * [<span data-ttu-id="c06a1-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="c06a1-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="c06a1-181">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="c06a1-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c06a1-182">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c06a1-182">Additional resources</span></span>

* [<span data-ttu-id="c06a1-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="c06a1-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="c06a1-184">RGPD: agregar un botón revocar consentimiento en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c06a1-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
