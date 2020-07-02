---
title: Transformación de web.config
author: rick-anderson
description: Obtenga información sobre cómo transformar el archivo web.config al publicar una aplicación ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: bebba7a72012b8be6257b14642bf130613627778
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404007"
---
# <a name="transform-webconfig"></a>Transformación de web.config

Por [Vijay Ramakrishnan](https://github.com/vijayrkn)

Las transformaciones en el archivo *web.config* se pueden aplicar automáticamente al publicarse una aplicación en función de:

* [Configuración de compilación](#build-configuration)
* [Profile](#profile)
* [Entorno](#environment)
* [Custom](#custom)

Estas transformaciones se producen en cualquiera de los escenarios de generación *web.config* siguientes:

* Generadas automáticamente por el SDK `Microsoft.NET.Sdk.Web`.
* Proporcionadas por el desarrollador en la [raíz del contenido](xref:fundamentals/index#content-root) de la aplicación.

## <a name="build-configuration"></a>Configuración de compilación

Las transformaciones de la configuración de compilación se ejecutan en primer lugar.

Incluya un archivo *web.{CONFIGURATION}.config* para cada [configuración de compilación (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) que requiera una transformación de *web.config*.

En el siguiente ejemplo, una variable de entorno específica de la configuración se establece en *web.Release.config*:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La transformación se aplica cuando la configuración se establece en *Release*:

```dotnetcli
dotnet publish --configuration Release
```

La propiedad MSBuild de la configuración es `$(Configuration)`.

## <a name="profile"></a>Perfil

Las transformaciones del perfil se ejecutan en segundo lugar, una vez transformada la [configuración de compilación](#build-configuration).

Incluya un archivo *web.{PROFILE}.config* para cada configuración de perfil que requiera una transformación de *web.config*.

En el siguiente ejemplo, una variable de entorno específica del perfil se establece en *web.FolderProfile.config* para un perfil de publicación de carpetas:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La transformación se aplica cuando el perfil es *FolderProfile*:

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

La propiedad MSBuild del nombre de perfil es `$(PublishProfile)`.

Si no se pasa ningún perfil, el nombre de perfil predeterminado es **FileSystem** y *web.FileSystem.config* se aplica si el archivo está presente en la raíz del contenido de la aplicación.

## <a name="environment"></a>Entorno

Las transformaciones del entorno se ejecutan en tercer lugar, una vez transformados la [configuración de compilación](#build-configuration) y el [perfil](#profile).

Incluya un archivo *web.{ENVIRONMENT}.config* para cada [entorno](xref:fundamentals/environments) que requiera una transformación de *web.config*.

En el siguiente ejemplo, una variable de entorno específica del entorno se establece en *web.Production.config* para el entorno de producción:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La transformación se aplica cuando el entorno es *Production*:

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

La propiedad MSBuild del entorno es `$(EnvironmentName)`.

Al publicar desde Visual Studio y usar un perfil de publicación, consulte <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.

La variable de entorno `ASPNETCORE_ENVIRONMENT` se agrega automáticamente al archivo *web.config* al especificarse el nombre del entorno.

## <a name="custom"></a>Personalizados

Las transformaciones personalizadas se ejecutan en último lugar, una vez transformados la [configuración de compilación](#build-configuration), el [perfil](#profile) y el [entorno](#environment).

Incluya un archivo *{CUSTOM_NAME}.transform* para cada configuración personalizada que requiera una transformación de *web.config*.

En el siguiente ejemplo, una variable de entorno de transformación personalizada se establece en *custom.transform*:

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

La transformación se aplica cuando la propiedad `CustomTransformFileName` se pasa al comando [dotnet publish](/dotnet/core/tools/dotnet-publish):

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

La propiedad MSBuild del nombre de perfil es `$(CustomTransformFileName)`.

## <a name="prevent-webconfig-transformation"></a>Evitar la transformación de web.config

Para evitar las transformaciones del archivo *web.config*, establezca la propiedad MSBuild `$(IsWebConfigTransformDisabled)`:

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a>Recursos adicionales

* [Sintaxis de transformación de Web.config para la implementación de proyectos de aplicación web](/previous-versions/dd465326(v=vs.100))
* [Sintaxis de transformación de Web.config para la implementación de proyectos web usando Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))
