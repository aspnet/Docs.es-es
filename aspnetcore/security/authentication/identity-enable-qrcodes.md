---
title: Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core
author: rick-anderson
description: Descubra cómo habilitar la generación de código QR para las aplicaciones de TOTP Authenticator que funcionan con la autenticación de dos factores ASP.NET Core.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060838"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="be75a-103">Habilitar la generación de código QR para las aplicaciones de TOTP Authenticator en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be75a-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="be75a-104">Los códigos QR requieren ASP.NET Core 2,0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="be75a-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="be75a-105">ASP.NET Core incluye compatibilidad con las aplicaciones de autenticador para la autenticación individual.</span><span class="sxs-lookup"><span data-stu-id="be75a-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="be75a-106">Las aplicaciones de autenticación en dos fases (2FA), con un algoritmo de contraseña de un solo uso (TOTP), son el enfoque recomendado del sector para 2FA.</span><span class="sxs-lookup"><span data-stu-id="be75a-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="be75a-107">se prefiere 2FA con TOTP a SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="be75a-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="be75a-108">Una aplicación autenticadora proporciona un código de 6 a 8 dígitos que los usuarios deben escribir después de confirmar su nombre de usuario y contraseña.</span><span class="sxs-lookup"><span data-stu-id="be75a-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="be75a-109">Normalmente, una aplicación autenticadora se instala en un smartphone.</span><span class="sxs-lookup"><span data-stu-id="be75a-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="be75a-110">Las plantillas de aplicación Web de ASP.NET Core admiten autenticadores, pero no proporcionan compatibilidad con la generación de QRCode.</span><span class="sxs-lookup"><span data-stu-id="be75a-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="be75a-111">Los generadores de QRCode facilitan la configuración de 2FA.</span><span class="sxs-lookup"><span data-stu-id="be75a-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="be75a-112">Este documento le guiará a través de la adición de la generación de [código QR](https://wikipedia.org/wiki/QR_code) a la página de configuración de 2FA.</span><span class="sxs-lookup"><span data-stu-id="be75a-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="be75a-113">La autenticación en dos fases no se realiza mediante un proveedor de autenticación externo, como [Google](xref:security/authentication/google-logins) o [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="be75a-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="be75a-114">Los inicios de sesión externos están protegidos por cualquier mecanismo que proporcione el proveedor de inicio de sesión externo.</span><span class="sxs-lookup"><span data-stu-id="be75a-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="be75a-115">Considere, por ejemplo, que el proveedor de autenticación de [Microsoft](xref:security/authentication/microsoft-logins) requiere una clave de hardware u otro enfoque 2FA.</span><span class="sxs-lookup"><span data-stu-id="be75a-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="be75a-116">Si las plantillas predeterminadas exigen "local" 2FA, los usuarios deberán cumplir dos enfoques de 2FA, lo que no es un escenario de uso frecuente.</span><span class="sxs-lookup"><span data-stu-id="be75a-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="be75a-117">Agregar códigos QR a la página de configuración de 2FA</span><span class="sxs-lookup"><span data-stu-id="be75a-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="be75a-118">Estas instrucciones usan *qrcode.js* del https://davidshimjs.github.io/qrcodejs/ repositorio.</span><span class="sxs-lookup"><span data-stu-id="be75a-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="be75a-119">Descargue el [qrcode.js biblioteca de JavaScript](https://davidshimjs.github.io/qrcodejs/) en la `wwwroot\lib` carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="be75a-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="be75a-120">Siga las instrucciones de [scaffolding :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) para generar */areas/ :::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="be75a-120">Follow the instructions in [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) to generate */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="be75a-121">En */areas/ :::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml* , busque la `Scripts` sección al final del archivo:</span><span class="sxs-lookup"><span data-stu-id="be75a-121">In */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="be75a-122">En *pages/Account/Manage/EnableAuthenticator. cshtml* ( :::no-loc(Razor)::: pages) o *views/Manage/EnableAuthenticator. cshtml* (MVC), busque la `Scripts` sección al final del archivo:</span><span class="sxs-lookup"><span data-stu-id="be75a-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (:::no-loc(Razor)::: Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="be75a-123">Actualice la `Scripts` sección para agregar una referencia a la `qrcodejs` biblioteca que ha agregado y una llamada para generar el código QR.</span><span class="sxs-lookup"><span data-stu-id="be75a-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="be75a-124">Debe tener el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="be75a-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="be75a-125">Elimine el párrafo que le vincula a estas instrucciones.</span><span class="sxs-lookup"><span data-stu-id="be75a-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="be75a-126">Ejecute la aplicación y asegúrese de que puede examinar el código QR y validar el código que el autenticador demuestra.</span><span class="sxs-lookup"><span data-stu-id="be75a-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="be75a-127">Cambiar el nombre del sitio en el código QR</span><span class="sxs-lookup"><span data-stu-id="be75a-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="be75a-128">El nombre del sitio en el código QR se toma del nombre del proyecto elegido al crear inicialmente el proyecto.</span><span class="sxs-lookup"><span data-stu-id="be75a-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="be75a-129">Puede cambiarlo si busca el `GenerateQrCodeUri(string email, string unformattedKey)` método en */areas/ :::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml.CS* .</span><span class="sxs-lookup"><span data-stu-id="be75a-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="be75a-130">El nombre del sitio en el código QR se toma del nombre del proyecto elegido al crear inicialmente el proyecto.</span><span class="sxs-lookup"><span data-stu-id="be75a-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="be75a-131">Puede cambiarlo si busca el `GenerateQrCodeUri(string email, string unformattedKey)` método en el archivo *pages/Account/Manage/EnableAuthenticator. cshtml. CS* ( :::no-loc(Razor)::: pages) o el archivo *Controllers/ManageController. CS* (MVC).</span><span class="sxs-lookup"><span data-stu-id="be75a-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (:::no-loc(Razor)::: Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="be75a-132">El código predeterminado de la plantilla tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="be75a-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode(":::no-loc(Razor)::: Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="be75a-133">El segundo parámetro de la llamada a `string.Format` es el nombre del sitio, tomado del nombre de la solución.</span><span class="sxs-lookup"><span data-stu-id="be75a-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="be75a-134">Se puede cambiar a cualquier valor, pero siempre debe ser una dirección URL codificada.</span><span class="sxs-lookup"><span data-stu-id="be75a-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="be75a-135">Usar una biblioteca de códigos QR diferente</span><span class="sxs-lookup"><span data-stu-id="be75a-135">Using a different QR Code library</span></span>

<span data-ttu-id="be75a-136">Puede reemplazar la biblioteca de códigos QR por su biblioteca preferida.</span><span class="sxs-lookup"><span data-stu-id="be75a-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="be75a-137">El código HTML contiene un `qrCode` elemento en el que se puede colocar un código QR por cualquier mecanismo que proporcione la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="be75a-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="be75a-138">La dirección URL con el formato correcto para el código QR está disponible en:</span><span class="sxs-lookup"><span data-stu-id="be75a-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="be75a-139">`AuthenticatorUri` propiedad del modelo.</span><span class="sxs-lookup"><span data-stu-id="be75a-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="be75a-140">`data-url` propiedad del `qrCodeData` elemento.</span><span class="sxs-lookup"><span data-stu-id="be75a-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="be75a-141">Desfase de tiempo de servidor y cliente de TOTP</span><span class="sxs-lookup"><span data-stu-id="be75a-141">TOTP client and server time skew</span></span>

<span data-ttu-id="be75a-142">La autenticación TOTP (basada en tiempo One-Time contraseña) depende del servidor y del dispositivo autenticador con una hora precisa.</span><span class="sxs-lookup"><span data-stu-id="be75a-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="be75a-143">Los tokens solo duran 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="be75a-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="be75a-144">Si se producen errores en los inicios de sesión de TOTP 2FA, compruebe que la hora del servidor es correcta y, preferiblemente, se ha sincronizado con un servicio NTP preciso.</span><span class="sxs-lookup"><span data-stu-id="be75a-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
