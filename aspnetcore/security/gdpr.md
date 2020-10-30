---
title: Compatibilidad de Reglamento general de protección de datos (RGPD) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo acceder a los puntos de extensión de RGPD en una aplicación Web de ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051010"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Compatibilidad con Reglamento general de protección de datos de la Unión Europea (RGPD) en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core proporciona API y plantillas para ayudar a cumplir algunos de los requisitos de la Reglamento general de protección de datos de la [UE (RGPD)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Las plantillas de proyecto incluyen puntos de extensión y marcado stub que se pueden reemplazar con la Directiva de privacidad y cookie uso.
* La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio.

Para habilitar la característica de consentimiento predeterminado, cookie como la que se encuentra en las plantillas de ASP.NET Core 2,2 en una aplicación de ASP.NET Core 3,0 plantillas generadas:

* Agregue `using Microsoft.AspNetCore.Http` a la lista de directivas using.
* Agregue [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a `Startup.ConfigureServices` y [use la Cookie Directiva](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) para `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Agregue el cookie consentimiento parcial al archivo *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Agregue el archivo *\_ Cookie ConsentPartial. cshtml* al proyecto:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Seleccione la versión ASP.NET Core 2,2 de este artículo para obtener información acerca de la cookie característica de consentimiento.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Las plantillas de proyecto incluyen puntos de extensión y marcado stub que se pueden reemplazar con la Directiva de privacidad y cookie uso.
* Una cookie característica de consentimiento le permite solicitar (y realizar un seguimiento) el consentimiento de los usuarios para almacenar información personal. Si un usuario no ha dado su consentimiento a la recopilación de datos y la aplicación tiene [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) establecido en, no se `true` cookie enviarán s no esenciales al explorador.
* Cookies se pueden marcar como esenciales. Los cookie s esenciales se envían al explorador incluso cuando el usuario no ha dado su consentimiento y el seguimiento está deshabilitado.
* [TempData y las cookie sesiones](#tempdata) no funcionan cuando el seguimiento está deshabilitado.
* La página [ Identity administrar](#pd) proporciona un vínculo para descargar y eliminar los datos de usuario.

La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite probar la mayoría de los puntos de extensión RGPD y las API agregadas a las plantillas ASP.net Core 2,1. Vea el archivo [Léame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obtener instrucciones de prueba.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>Compatibilidad de ASP.NET Core RGPD en el código generado por plantillas

Razor Las páginas y los proyectos de MVC creados con las plantillas de proyecto incluyen la siguiente compatibilidad con RGPD:

* [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) y [la Cookie Directiva de uso](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se establecen en la `Startup` clase.
* [Vista parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)de *\_ Cookie ConsentPartial. cshtml* . En este archivo se incluye un botón **Aceptar** . Cuando el usuario hace clic en el botón **Aceptar** , se proporciona el consentimiento a la tienda cookie .
* La página *pages/privacy. cshtml* o views */Home/privacy. cshtml* proporciona una página para detallar la Directiva de privacidad de su sitio. El archivo *\_ Cookie ConsentPartial. cshtml* genera un vínculo a la página de privacidad.
* En el caso de las aplicaciones creadas con cuentas de usuario individuales, la página Administrar proporciona vínculos para descargar y eliminar los [datos personales](#pd)de los usuarios.

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolicyOptions y uso de la Cookie Directiva

[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) se inicializan en `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Usar Cookie ](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) Se llama a la Directiva en `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieVista parcial de ConsentPartial. cshtml

La vista parcial de *\_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Este:

* Obtiene el estado de seguimiento del usuario. Si la aplicación está configurada para requerir el consentimiento, el usuario debe dar su consentimiento antes de cookie que se pueda realizar el seguimiento de. Si se requiere el consentimiento, el cookie Panel de consentimiento se fija en la parte superior de la barra de navegación creada por el archivo *\_ layout. cshtml* .
* Proporciona un `<p>` elemento HTML para resumir la privacidad y la cookie Directiva de uso.
* Proporciona un vínculo a la página de privacidad o vista donde puede obtener detalles de la Directiva de privacidad del sitio.

## <a name="essential-no-loccookies"></a>Essential cookie s

Si no cookie se ha proporcionado el consentimiento para la tienda, solo cookie se envían al explorador los s marcados como esenciales. El código siguiente hace que sea cookie esencial:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>El proveedor TempData y el estado de sesión cookie s no son esenciales

El [proveedor TempData](xref:fundamentals/app-state#tempdata) cookie no es esencial. Si el seguimiento está deshabilitado, el proveedor TempData no funciona. Para habilitar el proveedor TempData cuando el seguimiento está deshabilitado, marque TempData cookie como Essential en `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Estado](xref:fundamentals/app-state) cookie de sesión s no son esenciales. El estado de sesión no funciona cuando el seguimiento está deshabilitado. El código siguiente hace que las sesiones sean cookie esenciales:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Datos personales

ASP.NET Core aplicaciones creadas con cuentas de usuario individuales incluyen código para descargar y eliminar los datos personales.

Seleccione el nombre de usuario y, a continuación, seleccione **datos personales** :

![Página administrar datos personales](gdpr/_static/pd.png)

Notas:

* Para generar el `Account/Manage` código, consulte [scaffold Identity ](xref:security/authentication/scaffold-identity).
* Los vínculos de **eliminación** y **descarga** solo actúan sobre los datos de identidad predeterminados. Las aplicaciones que crean datos de usuario personalizados deben extenderse para eliminar o descargar los datos de usuario personalizados. Para obtener más información, vea [Agregar, descargar y eliminar datos de usuario personalizados Identity en ](xref:security/authentication/add-user-data).
* Los tokens guardados para el usuario que se almacenan en la Identity tabla de base de datos `AspNetUserTokens` se eliminan cuando se elimina el usuario mediante el comportamiento de eliminación en cascada debido a la [clave externa](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* La [autenticación de proveedor externo](xref:security/authentication/social/index), como Facebook y Google, no está disponible antes de que cookie se acepte la Directiva.

::: moniker-end

## <a name="encryption-at-rest"></a>Cifrado en reposo

Algunas bases de datos y mecanismos de almacenamiento permiten el cifrado en reposo. Cifrado en reposo:

* Cifra automáticamente los datos almacenados.
* Cifra sin configuración, programación u otro trabajo para el software que tiene acceso a los datos.
* Es la opción más sencilla y más segura.
* Permite a la base de datos administrar las claves y el cifrado.

Por ejemplo:

* Microsoft SQL y Azure SQL proporcionan [cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure cifra la base de datos de forma predeterminada](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Los blobs de Azure, los archivos, las tablas y los Queue Storage están cifrados de forma predeterminada](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

En el caso de las bases de datos que no proporcionan cifrado integrado en reposo, es posible que pueda usar el cifrado de disco para proporcionar la misma protección. Por ejemplo:

* [BitLocker para Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Recursos adicionales

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [RGPD: agregar un botón revocar consentimiento en ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
