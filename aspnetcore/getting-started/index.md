---
title: Introducción a ASP.NET Core
author: rick-anderson
description: Tutorial breve que crea y ejecuta una aplicación Hola mundo básica mediante ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
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
uid: getting-started
ms.openlocfilehash: afded8890afe3b8f7b1d0b5634fc7764906bc9d7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635013"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Tutorial: Introducción a ASP.NET Core

En este tutorial se muestra cómo crear y ejecutar una aplicación web ASP.NET Core con la CLI de .NET Core.

Aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación web.
> * Confíe en el certificado de desarrollo.
> * Ejecutar la aplicación.
> * Editar una página de Razor.

Al final, tendrá una aplicación web en funcionamiento ejecutándose en el equipo local.

![Página principal de aplicación web](_static/home-page.png)

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Crear un proyecto de aplicación web

Abra un shell de comandos y escriba el siguiente comando:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

El comando anterior:

* Crea una nueva aplicación web.  
* El parámetro `-o aspnetcoreapp` crea un directorio llamado *aspnetcoreapp* con los archivos de código fuente de la aplicación.

### <a name="trust-the-development-certificate"></a>Confíe en el certificado de desarrollo

Confíe en el certificado de desarrollo HTTPS:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

El comando anterior muestra el siguiente cuadro de diálogo:

![Cuadro de diálogo de advertencia de seguridad](~/getting-started/_static/cert.png)

Si acepta confiar en el certificado de desarrollo, seleccione **Sí**.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

El comando anterior muestra el siguiente mensaje:

*Se ha solicitado que confíe en el certificado de desarrollo HTTPS. Si el certificado todavía no es de confianza, se ejecutará el comando siguiente:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`.

Este comando podría solicitarle la contraseña para instalar el certificado en la cadena de claves del sistema. Si acepta confiar en el certificado de desarrollo, escriba la contraseña.

# <a name="linux"></a>[Linux](#tab/linux)

Consulte la documentación correspondiente a su distribución Linux sobre cómo confiar en el certificado de desarrollo HTTPS.

---

Para más información, consulte [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS ](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute los comandos siguientes:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Después de que el shell de comandos indique que se ha iniciado la aplicación, vaya a `https://localhost:5001`.

## <a name="edit-a-no-locrazor-page"></a>Edición de una página de Razor Pages

Abra *Pages/Index.cshtml* y modifique y guarde la página con el siguiente marcado resaltado:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Vaya a `https://localhost:5001`, actualice la página y confirme que los cambios aparecen reflejados.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a:

> [!div class="checklist"]
> * Crear un proyecto de aplicación web.
> * Confíe en el certificado de desarrollo.
> * Ejecute el proyecto.
> * Realizar un cambio.

Para obtener más información sobre ASP.NET Core, vea la ruta de aprendizaje recomendada en la introducción:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
