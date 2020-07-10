---
title: Compatibilidad de Reglamento general de protección de datos (RGPD) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo acceder a los puntos de extensión de RGPD en una aplicación Web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 8a7041a976ea9f0e99bfd1eba792d0e919eaf6d3
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212828"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="e3bf2-103">Compatibilidad con Reglamento general de protección de datos de la Unión Europea (RGPD) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3bf2-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="e3bf2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e3bf2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e3bf2-105">ASP.NET Core proporciona API y plantillas para ayudar a cumplir algunos de los requisitos de la Reglamento general de protección de datos de la [UE (RGPD)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e3bf2-106">Las plantillas de proyecto incluyen puntos de extensión y marcado stub que puede reemplazar con la Directiva de privacidad y uso de cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="e3bf2-107">La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="e3bf2-108">Para habilitar la característica de consentimiento de cookies predeterminada, como la que se encuentra en las plantillas de ASP.NET Core 2,2 en una aplicación de ASP.NET Core 3,0 plantillas generadas:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="e3bf2-109">Agregue `using Microsoft.AspNetCore.Http` a la lista de directivas using.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="e3bf2-110">Agregue [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` y [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) a `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="e3bf2-111">Agregue el consentimiento de la cookie parcial al archivo *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="e3bf2-112">Agregue el archivo \* \_ CookieConsentPartial. cshtml\* al proyecto:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="e3bf2-113">Seleccione la versión ASP.NET Core 2,2 de este artículo para obtener información acerca de la característica de consentimiento de cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="e3bf2-114">Las plantillas de proyecto incluyen puntos de extensión y marcado stub que puede reemplazar con la Directiva de privacidad y uso de cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="e3bf2-115">Una característica de consentimiento de cookies le permite solicitar (y realizar un seguimiento) el consentimiento de los usuarios para almacenar información personal.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="e3bf2-116">Si un usuario no ha dado su consentimiento a la recopilación de datos y la aplicación tiene [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) establecido en `true` , las cookies no esenciales no se envían al explorador.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="e3bf2-117">Las cookies se pueden marcar como esenciales.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="e3bf2-118">Las cookies esenciales se envían al explorador incluso cuando el usuario no ha dado su consentimiento y el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="e3bf2-119">[TempData y las cookies de sesión](#tempdata) no funcionan cuando el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="e3bf2-120">La página [ Identity administrar](#pd) proporciona un vínculo para descargar y eliminar los datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="e3bf2-121">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite probar la mayoría de los puntos de extensión RGPD y las API agregadas a las plantillas ASP.net Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="e3bf2-122">Vea el archivo [Léame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obtener instrucciones de prueba.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="e3bf2-123">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e3bf2-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="e3bf2-124">Compatibilidad de ASP.NET Core RGPD en el código generado por plantillas</span><span class="sxs-lookup"><span data-stu-id="e3bf2-124">ASP.NET Core GDPR support in template-generated code</span></span>

Razor<span data-ttu-id="e3bf2-125">Las páginas y los proyectos de MVC creados con las plantillas de proyecto incluyen la siguiente compatibilidad con RGPD:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-125"> Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="e3bf2-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) y [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se establecen en la `Startup` clase.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="e3bf2-127">[Vista parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)de \* \_ CookieConsentPartial. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="e3bf2-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="e3bf2-128">En este archivo se incluye un botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="e3bf2-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="e3bf2-129">Cuando el usuario hace clic en el botón **Aceptar** , se proporciona el consentimiento para almacenar las cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="e3bf2-130">La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="e3bf2-131">El archivo \* \_ CookieConsentPartial. cshtml\* genera un vínculo a la página de privacidad.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="e3bf2-132">En el caso de las aplicaciones creadas con cuentas de usuario individuales, la página Administrar proporciona vínculos para descargar y eliminar los [datos personales](#pd)de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="e3bf2-133">CookiePolicyOptions y UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="e3bf2-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="e3bf2-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) se inicializan en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="e3bf2-135">Se llama a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) en `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="e3bf2-136">\_Vista parcial de CookieConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="e3bf2-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="e3bf2-137">La vista parcial de \* \_ CookieConsentPartial. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="e3bf2-138">Este:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-138">This partial:</span></span>

* <span data-ttu-id="e3bf2-139">Obtiene el estado de seguimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="e3bf2-140">Si la aplicación está configurada para requerir el consentimiento, el usuario debe dar su consentimiento antes de que se pueda realizar el seguimiento de las cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="e3bf2-141">Si se requiere el consentimiento, el panel de consentimiento de cookies se fija en la parte superior de la barra de navegación creada por el archivo \* \_ layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="e3bf2-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="e3bf2-142">Proporciona un `<p>` elemento HTML para resumir la privacidad y la Directiva de uso de cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="e3bf2-143">Proporciona un vínculo a la página de privacidad o vista donde puede obtener detalles de la Directiva de privacidad del sitio.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="e3bf2-144">Cookies esenciales</span><span class="sxs-lookup"><span data-stu-id="e3bf2-144">Essential cookies</span></span>

<span data-ttu-id="e3bf2-145">Si no se ha proporcionado el consentimiento para almacenar las cookies, solo se envían al explorador las cookies marcadas como esenciales.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="e3bf2-146">El código siguiente hace que una cookie sea esencial:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="e3bf2-147">Las cookies del proveedor TempData y del estado de sesión no son esenciales</span><span class="sxs-lookup"><span data-stu-id="e3bf2-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="e3bf2-148">La cookie del [proveedor TempData](xref:fundamentals/app-state#tempdata) no es esencial.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="e3bf2-149">Si el seguimiento está deshabilitado, el proveedor TempData no funciona.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="e3bf2-150">Para habilitar el proveedor TempData cuando el seguimiento está deshabilitado, marque la cookie TempData como esencial en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e3bf2-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="e3bf2-151">Las cookies de [Estado de sesión](xref:fundamentals/app-state) no son esenciales.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="e3bf2-152">El estado de sesión no funciona cuando el seguimiento está deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="e3bf2-153">El código siguiente hace que las cookies de sesión sean esenciales:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="e3bf2-154">Datos personales</span><span class="sxs-lookup"><span data-stu-id="e3bf2-154">Personal data</span></span>

<span data-ttu-id="e3bf2-155">ASP.NET Core aplicaciones creadas con cuentas de usuario individuales incluyen código para descargar y eliminar los datos personales.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="e3bf2-156">Seleccione el nombre de usuario y, a continuación, seleccione **datos personales**:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-156">Select the user name and then select **Personal data**:</span></span>

![Página administrar datos personales](gdpr/_static/pd.png)

<span data-ttu-id="e3bf2-158">Notas:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-158">Notes:</span></span>

* <span data-ttu-id="e3bf2-159">Para generar el `Account/Manage` código, consulte [scaffold Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="e3bf2-160">Los vínculos de **eliminación** y **descarga** solo actúan sobre los datos de identidad predeterminados.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="e3bf2-161">Las aplicaciones que crean datos de usuario personalizados deben extenderse para eliminar o descargar los datos de usuario personalizados.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="e3bf2-162">Para obtener más información, vea [Agregar, descargar y eliminar datos de usuario personalizados Identity en ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="e3bf2-163">Los tokens guardados para el usuario que se almacenan en la Identity tabla de base de datos `AspNetUserTokens` se eliminan cuando se elimina el usuario mediante el comportamiento de eliminación en cascada debido a la [clave externa](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="e3bf2-164">La [autenticación de proveedor externo](xref:security/authentication/social/index), como Facebook y Google, no está disponible antes de que se acepte la Directiva de cookies.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="e3bf2-165">Cifrado en reposo</span><span class="sxs-lookup"><span data-stu-id="e3bf2-165">Encryption at rest</span></span>

<span data-ttu-id="e3bf2-166">Algunas bases de datos y mecanismos de almacenamiento permiten el cifrado en reposo.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="e3bf2-167">Cifrado en reposo:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-167">Encryption at rest:</span></span>

* <span data-ttu-id="e3bf2-168">Cifra automáticamente los datos almacenados.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="e3bf2-169">Cifra sin configuración, programación u otro trabajo para el software que tiene acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="e3bf2-170">Es la opción más sencilla y más segura.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="e3bf2-171">Permite a la base de datos administrar las claves y el cifrado.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="e3bf2-172">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-172">For example:</span></span>

* <span data-ttu-id="e3bf2-173">Microsoft SQL y Azure SQL proporcionan [cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="e3bf2-174">SQL Azure cifra la base de datos de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="e3bf2-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="e3bf2-175">[Los blobs de Azure, los archivos, las tablas y los Queue Storage están cifrados de forma predeterminada](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="e3bf2-176">En el caso de las bases de datos que no proporcionan cifrado integrado en reposo, es posible que pueda usar el cifrado de disco para proporcionar la misma protección.</span><span class="sxs-lookup"><span data-stu-id="e3bf2-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="e3bf2-177">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-177">For example:</span></span>

* [<span data-ttu-id="e3bf2-178">BitLocker para Windows Server</span><span class="sxs-lookup"><span data-stu-id="e3bf2-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="e3bf2-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="e3bf2-179">Linux:</span></span>
  * [<span data-ttu-id="e3bf2-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="e3bf2-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="e3bf2-181">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="e3bf2-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3bf2-182">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e3bf2-182">Additional resources</span></span>

* [<span data-ttu-id="e3bf2-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="e3bf2-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="e3bf2-184">RGPD: agregar un botón revocar consentimiento en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3bf2-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
