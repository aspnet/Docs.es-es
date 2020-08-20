---
title: Perfiles de publicación (.pubxml) de Visual Studio para la implementación de aplicaciones ASP.NET Core
author: rick-anderson
description: Aprenda a crear perfiles de publicación en Visual Studio y usarlos para administrar implementaciones de aplicaciones ASP.NET Core en diversos destinos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: a7bbc3f3a0a0d5a320bd290d325f0c01390828b4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634168"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Perfiles de publicación (.pubxml) de Visual Studio para la implementación de aplicaciones ASP.NET Core

Por [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) y [Rick Anderson](https://twitter.com/RickAndMSFT)

Este documento se centra en el uso de Visual Studio 2019 o versiones posteriores para crear y usar perfiles de publicación. Los perfiles de publicación creados con Visual Studio se pueden usar con MSBuild y Visual Studio. Para obtener instrucciones sobre la publicación en Azure, consulte <xref:tutorials/publish-to-azure-webapp-using-vs>.

El comando `dotnet new mvc` produce un archivo de proyecto que contiene el siguiente [elemento \<Project> de nivel de raíz](/visualstudio/msbuild/project-element-msbuild):

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

El atributo `Sdk` del elemento anterior `<Project>` importa las [propiedades](/visualstudio/msbuild/msbuild-properties) y [objetivos](/visualstudio/msbuild/msbuild-targets) de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* y *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivamente. La ubicación predeterminada de `$(MSBuildSDKsPath)` (con Visual Studio 2019 Enterprise) es la carpeta *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` ([SDK web](xref:razor-pages/web-sdk)) depende de otros SDK, como `Microsoft.NET.Sdk` ([SDK de .NET Core](/dotnet/core/project-sdk/msbuild-props)) y `Microsoft.NET.Sdk.Razor` ([SDK de Razor](xref:razor-pages/sdk)). Se importan las propiedades y objetivos de MSBuild asociados con cada SDK dependiente. Los destinos de publicación importan el conjunto adecuado de destinos en función del método de publicación usado.

Cuando se carga un proyecto en Visual Studio o MSBuild, se llevan a cabo las siguientes acciones generales:

* Compilación del proyecto
* Cálculo de los archivos para la publicación
* Publicación de los archivos en el destino

## <a name="compute-project-items"></a>Cálculo de los elementos del proyecto

Cuando se carga el proyecto, se procesan los [elementos del proyecto de MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (archivos). El tipo de elemento determina cómo se procesa el archivo. De forma predeterminada, los archivos *.cs* se incluyen en la lista de elementos `Compile`. Después se compilan los archivos de la lista de elementos `Compile`.

La lista de elementos `Content` contiene archivos que se van a publicar, además de los resultados de compilación. De forma predeterminada, los archivos que coinciden con los patrones `wwwroot\**`, `**\*.config` y `**\*.json` se incluyen en la lista de elementos `Content`. Por ejemplo, el [patrón global](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` coincide con los archivos de la carpeta *wwwroot* y de sus subcarpetas.

::: moniker range=">= aspnetcore-3.0"

El [SDK web](xref:razor-pages/web-sdk) importa el [SDK de Razor](xref:razor-pages/sdk). Como resultado, los archivos que coinciden con los patrones `**\*.cshtml` y `**\*.razor` se incluyen también en la lista de elementos `Content`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

El [SDK web](xref:razor-pages/web-sdk) importa el [SDK de Razor](xref:razor-pages/sdk). Como resultado, los archivos que coinciden con el patrón `**\*.cshtml` y se incluye también en la lista de elementos `Content`.

::: moniker-end

Para agregar explícitamente un archivo a la lista de publicación, agregue el archivo directamente en el archivo *.csproj*, como se muestra en la sección [Archivos de inclusión](#include-files).

Al seleccionar el botón **Publicar** en Visual Studio o al publicar desde la línea de comandos:

* Se calculan los elementos/propiedades (los archivos que se deben compilar).
* **Solo Visual Studio**: Se han restaurado los paquetes NuGet. (la restauración debe ser explícita por parte del usuario en la CLI).
* Se compila el proyecto.
* Se calculan los elementos de publicación (los archivos que se deben publicar).
* Se publica el proyecto (los archivos calculados se copian en el destino de publicación).

Cuando hace referencia a un proyecto de ASP.NET Core `Microsoft.NET.Sdk.Web` en el archivo de proyecto, se coloca un archivo *app_offline.htm* en la raíz del directorio de la aplicación web. Cuando el archivo está presente, el módulo de ASP.NET Core cierra correctamente la aplicación y proporciona el archivo *app_offline.htm* durante la implementación. Para más información, vea [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm) (Referencia de configuración del módulo de ASP.NET Core).

## <a name="basic-command-line-publishing"></a>Publicación básica de línea de comandos

La publicación de línea de comandos funciona en todas las plataformas admitidas de .NET Core y no se requiere Visual Studio. En los ejemplos siguientes, se ejecuta el comando [dotnet publish](/dotnet/core/tools/dotnet-publish) de la CLI de .NET Core desde el directorio del proyecto (que contiene el archivo *.csproj*). Si la carpeta del proyecto no es el directorio de trabajo actual, pase de forma explícita la ruta de acceso del archivo del proyecto. Por ejemplo:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Ejecute los comandos siguientes para crear y publicar una aplicación web:

```dotnetcli
dotnet new mvc
dotnet publish
```

Con el comando `dotnet publish` se genera una variación de la salida siguiente:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

El formato de la carpeta de publicación predeterminado es *bin\Debug\\{MONIKER DE LA PLATAFORMA DE DESTINO}\publish\\* . Por ejemplo, *bin\Debug\netcoreapp2.2\publish\\* .

El siguiente comando especifica una compilación `Release` y el directorio de publicación:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Con el comando `dotnet publish` se llama a MSBuild, lo que invoca el destino `Publish`. Todos los parámetros pasados a `dotnet publish` se pasan a MSBuild. Los parámetros `-c` y `-o` se asignan respectivamente a las propiedades `Configuration` y `OutputPath` de MSBuild.

Se pueden pasar propiedades de MSBuild mediante cualquiera de los siguientes formatos:

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Por ejemplo, el comando siguiente publica una compilación `Release` en un recurso compartido de red. El recurso compartido de red se especifica con barras diagonales ( *//r8/* ) y funciona en todas las plataformas compatibles de .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Confirme que la aplicación publicada para la implementación no se está ejecutando. Los archivos de la carpeta *publish* quedan bloqueados mientras se ejecuta la aplicación. No se puede llevar a cabo la implementación porque no se pueden copiar los archivos bloqueados.

## <a name="publish-profiles"></a>Publicar los perfiles

En esta sección se usa Visual Studio 2019 o versiones posteriores para crear un perfil de publicación. Una vez creado el perfil, es posible realizar la publicación desde Visual Studio o la línea de comandos. Los perfiles de publicación pueden simplificar el proceso de publicación, y puede existir cualquier número de perfiles.

Cree un perfil de publicación en Visual Studio mediante la selección de una de las rutas de acceso siguientes:

* Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar**.
* Seleccione **Publicar {NOMBRE DEL PROYECTO}** en el menú **Compilar**.

Aparece la pestaña **Publicar** de la página de funcionalidades de la aplicación. Si el proyecto no contiene ningún perfil de publicación, se muestra la página **Elegir un destino de publicación**. Se le pide que seleccione uno de los siguientes destinos de publicación:

* Azure App Service
* Azure App Service en Linux
* Azure Virtual Machines
* Carpeta
* IIS, FTP, Web Deploy (para cualquier servidor web)
* Perfil de importación

Para determinar el destino de publicación más adecuado, consulte [Qué opciones de publicación son las adecuadas para mí](/visualstudio/ide/not-in-toc/web-publish-options).

Cuando seleccione el destino de publicación **Carpeta**, especifique una ruta de acceso a la carpeta para almacenar los recursos publicados. La ruta de carpeta predeterminada es *bin\\{CONFIGURACIÓN DEL PROYECTO}\\{MONIKER DE LA PLATAFORMA DE DESTINO}\publish\\* . Por ejemplo, *bin\Release\netcoreapp2.2\publish\\* . Seleccione el botón **Crear perfil** para terminar.

Una vez que se crea un perfil de publicación, cambia el contenido de la pestaña **Publicar**. El perfil recién creado aparece en una lista desplegable. Debajo de la lista desplegable, seleccione **Crear nuevo perfil** para crear otro perfil nuevo.

La herramienta de publicación de Visual Studio genera un archivo MSBuild denominado *Properties/PublishProfiles/{NOMBRE DEL PERFIL}.pubxml* en el que se describe el perfil de publicación. El archivo *.pubxml*:

* Contiene los valores de la configuración de publicación y es utilizado por el proceso de publicación.
* Se puede modificar para personalizar el proceso de compilación y publicación.

Al publicar en un destino de Azure, el archivo *.pubxml* contiene el identificador de suscripción de Azure. Con ese tipo de destino, no se recomienda agregar este archivo al control de código fuente. Al publicar en un destino que no sea Azure, es seguro insertar el archivo *.pubxml* en el repositorio.

La información confidencial (por ejemplo, la contraseña de publicación) se cifra en cada usuario y máquina. Este se almacena en el archivo *Properties/PublishProfiles/{NOMBRE DEL PERFIL}.pubxml.user*. Como este archivo puede contener información confidencial, no se debe insertar en el repositorio de control del código fuente.

Para obtener información general sobre cómo publicar una aplicación web ASP.NET Core, vea <xref:host-and-deploy/index>. Las tareas y los destinos de MSBuild necesarios para publicar una aplicación web de ASP.NET Core son de código abierto en el repositorio [dotnet/websdk](https://github.com/dotnet/websdk).

Los siguientes comandos pueden usar perfiles de publicación de carpeta, MSDeploy y [Kudu](https://github.com/projectkudu/kudu/wiki). Ya que MSDeploy no tiene compatibilidad multiplataforma, las siguientes opciones de MSDeploy solo se admiten en Windows.

**Carpeta (funciona entre plataformas):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Paquete MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

En los ejemplos anteriores:

* `dotnet publish` y `dotnet build` admiten API de Kudu para publicar en Azure desde cualquier plataforma. La publicación de Visual Studio admite API de Kudu, pero es compatible con WebSDK para la publicación multiplataforma en Azure.
* No pase `DeployOnBuild` al comando `dotnet publish`.

Para más información, consulte [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Agregue un perfil de publicación a la carpeta del proyecto *Properties/PublishProfiles* con el contenido siguiente:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Ejemplo de publicación de carpetas

Al publicar con un perfil llamado *FolderProfile* , use cualquiera de los siguientes comandos:

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Con el comando [dotnet build](/dotnet/core/tools/dotnet-build) de la CLI de .NET Core se llama a `msbuild` para ejecutar el proceso de compilación y publicación. Los comandos `dotnet build` y `msbuild` son equivalentes cuando se pasa un perfil de carpeta. Al llamar a `msbuild` directamente en Windows, se usa la versión de .NET Framework de MSBuild. Llamada a `dotnet build` en un perfil que no sea de carpeta:

* Invoca `msbuild`, que usa MSDeploy.
* Produce un error (incluso cuando se ejecuta en Windows). Para publicar con un perfil que no es de carpeta, llame directamente a `msbuild`.

El siguiente perfil de publicación de carpeta se creó con Visual Studio y se publica en un recurso compartido de red:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

En el ejemplo anterior:

* La propiedad `<ExcludeApp_Data>` está presente simplemente para satisfacer un requisito de esquema XML. La propiedad `<ExcludeApp_Data>` no tiene ningún efecto en el proceso de publicación, aunque haya una carpeta *App_Data* en la raíz del proyecto. La carpeta *App_Data* no recibe un tratamiento especial, como sí sucede en los proyectos de ASP.NET 4.x.
* La propiedad `<LastUsedBuildConfiguration>` se establece en `Release`. Al efectuar una publicación en Visual Studio, el valor de `<LastUsedBuildConfiguration>` se establece con el valor cuando se inicia el proceso de publicación. `<LastUsedBuildConfiguration>` es especial y no se debe reemplazar en un archivo importado de MSBuild. Sin embargo, esta propiedad se puede invalidar desde la línea de comandos mediante uno de los métodos siguientes.
  * Con la CLI de .NET Core:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Con MSBuild:

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Vea [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) (MSBuild: Cómo establecer la propiedad de configuración).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Publicar en un punto de conexión de MSDeploy desde la línea de comandos

En el ejemplo siguiente se utiliza una aplicación web ASP.NET Core de ejemplo creada mediante Visual Studio y denominada *AzureWebApp*. Se agrega un perfil de publicación de aplicaciones de Azure con Visual Studio. Para obtener más información sobre cómo crear un perfil, consulte la sección [Perfiles de publicación](#publish-profiles).

Para implementar la aplicación con un perfil de publicación, ejecute el comando `msbuild` desde un **símbolo del sistema para desarrolladores** de Visual Studio. El símbolo del sistema se encuentra disponible en la carpeta *Visual Studio* del menú **Inicio**, en la barra de tareas de Windows. Para facilitar el acceso, puede agregar el símbolo del sistema al menú **Herramientas** de Visual Studio. Para más información, consulte [Símbolo del sistema para desarrolladores de Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild usa la sintaxis de comando siguiente:

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: ruta de acceso al archivo de proyecto de la aplicación.
* `{PROFILE}`: nombre del perfil de publicación.
* `{USERNAME}`: nombre de usuario de MSDeploy. `{USERNAME}` está en el perfil de publicación.
* `{PASSWORD}`: contraseña de MSDeploy. Obtenga el valor `{PASSWORD}` en el archivo *{PROFILE}.PublishSettings*. Descargue el archivo *.PublishSettings* desde:
  * **Explorador de soluciones**: Seleccione **Ver** > **Cloud Explorer**. Conéctese con su suscripción de Azure. Abra **App Services**. Haga clic con el botón derecho en la aplicación. Seleccione **Descargar perfil de publicación**.
  * Azure Portal: Haga clic en **Obtener perfil de publicación**, en el panel **Información general** de la aplicación web.

En el ejemplo siguiente se usa un perfil de publicación denominado *AzureWebApp - Web Deploy*:

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

También se puede usar un perfil de publicación ejecutando el comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) de la CLI de .NET Core en un shell de comandos de Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> El comando `dotnet msbuild` es multiplataforma y permite compilar aplicaciones ASP.NET Core en macOS y Linux. Sin embargo, MSBuild en macOS y Linux no permite implementar una aplicación en Azure ni en otros puntos de conexión de MSDeploy.

## <a name="set-the-environment"></a>Establecimiento del entorno

Incluya la propiedad `<EnvironmentName>` en el perfil de publicación ( *.pubxml*) o el archivo del proyecto para configurar el [entorno](xref:fundamentals/environments) de la aplicación:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Si necesita transformaciones de *web.config* (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Archivos de exclusión

Al publicar aplicaciones web de ASP.NET Core, se incluyen los siguientes recursos:

* Artefactos de compilación
* Las carpetas y archivos que coinciden con los siguientes patrones globales de uso:
  * `**\*.config` (por ejemplo, *web.config*)
  * `**\*.json` (por ejemplo, *appsettings.json*)
  * `wwwroot\**`

MSBuild admite los [patrones globales](https://gruntjs.com/configuring-tasks#globbing-patterns). Por ejemplo, el siguiente elemento `<Content>` suprime la copia de los archivos de texto ( *.txt*) de la carpeta *wwwroot/content* y de sus subcarpetas:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

El marcado anterior se puede agregar a un perfil de publicación o al archivo *.csproj*. Si se agrega al archivo *.csproj*, la regla se agrega a todos los perfiles de publicación del proyecto.

El siguiente elemento `<MsDeploySkipRules>` excluye todos los archivos de la carpeta *wwwroot/content*:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` no eliminará del sitio de implementación los destinos *skip*. Los archivos y carpetas destinados a `<Content>` se eliminan del sitio de implementación. Por ejemplo, suponga que una aplicación web implementada tenía los siguientes archivos:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Si se agregan los siguientes elementos `<MsDeploySkipRules>`, esos archivos no se eliminarán en el sitio de implementación.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Los elementos `<MsDeploySkipRules>` anteriores impiden que se implementen los archivos *omitidos*. Una vez que se implementan esos archivos, no se eliminarán.

El siguiente elemento `<Content>` elimina los archivos de destino en el sitio de implementación:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

El uso de la implementación de línea de comandos con el elemento `<Content>` anterior produce una variación de la siguiente salida:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Archivos de inclusión

En la siguientes secciones se describen diferentes enfoques para la inclusión de archivos en el momento de la publicación. En la sección [Inclusión de archivos general](#general-file-inclusion) se usa el elemento `DotNetPublishFiles`, que se proporciona mediante un archivo de destinos de publicación en el [SDK web](xref:razor-pages/web-sdk). En la sección [Inclusión de archivos selectiva](#selective-file-inclusion) se usa el elemento `ResolvedFileToPublish`, que se proporciona mediante un archivo de destinos de publicación en el [SDK de .NET Core](/dotnet/core/project-sdk/msbuild-props). Dado que el SDK web depende del SDK de .NET Core, se puede usar cualquier elemento en un proyecto de ASP.NET Core.

### <a name="general-file-inclusion"></a>Inclusión de archivos general

En el siguiente ejemplo, en el elemento `<ItemGroup>` se muestra cómo copiar una carpeta que se encuentra fuera del directorio del proyecto en una carpeta del sitio publicado. Los archivos agregados al elemento `<ItemGroup>` del marcado siguiente se incluyen de forma predeterminada.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

El marcado anterior:

* Se puede agregar al archivo *.csproj* o al perfil de publicación. Si se agrega al archivo *.csproj*, se incluye en todos los perfiles de publicación del proyecto.
* Declara un elemento `_CustomFiles` para almacenar los archivos coincidentes con el patrón global del atributo `Include`. La carpeta *images* a la que se hace referencia en el patrón se encuentra fuera del directorio del proyecto. Una [propiedad reservada](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), denominada `$(MSBuildProjectDirectory)`, se resuelve en la ruta de acceso absoluta del archivo de proyecto.
* Proporciona una lista de los archivos para el elemento `DotNetPublishFiles`. El elemento `<DestinationRelativePath>` de la lista aparece vacío de forma predeterminada. El valor predeterminado se reemplaza en el marcado y usa [metadatos de elementos conocidos](/visualstudio/msbuild/msbuild-well-known-item-metadata) como `%(RecursiveDir)`. El texto interno representa la carpeta *wwwroot/images* del sitio publicado.

### <a name="selective-file-inclusion"></a>Inclusión de archivos selectiva

En el marcado resaltado del siguiente ejemplo se muestra lo siguiente:

* La copia un archivo que se encuentra fuera del proyecto en la carpeta *wwwroot* del sitio publicado. Se mantiene el nombre del archivo *ReadMe2.md*.
* La exclusión de la carpeta *wwwroot\Content*.
* La exclusión de *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

En el ejemplo anterior se usa el elemento `ResolvedFileToPublish`, cuyo comportamiento predeterminado consiste siempre en copiar los archivos proporcionados en el atributo `Include` en el sitio publicado. Invalide el comportamiento predeterminado mediante la inclusión de un elemento secundario `<CopyToPublishDirectory>` con el texto interno de `Never` o `PreserveNewest`. Por ejemplo:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Para ver más ejemplos de implementación, consulte el [archivo Léame del repositorio de WebSDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Ejecutar un destino antes o después de la publicación

Los destinos `BeforePublish` y `AfterPublish` ejecutan un destino antes o después del destino de publicación. Agregue los siguientes elementos al perfil de publicación para registrar mensajes de la consola antes y después de la publicación:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Publicación en un servidor mediante un certificado que no es de confianza

Agregue la propiedad `<AllowUntrustedCertificate>` con un valor de `True` al perfil de publicación:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Servicio Kudu

Para ver los archivos de la implementación de una aplicación web de Azure App Service, use el [servicio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Anexe el token `scm` al nombre de la aplicación web. Por ejemplo:

| Resolución                                    | Resultado       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Aplicación web      |
| `http://mysite.scm.azurewebsites.net/` | Servicio Kudu |

Seleccione el elemento de menú [Consola de depuración](https://github.com/projectkudu/kudu/wiki/Kudu-console) para ver, editar, eliminar o agregar archivos.

## <a name="additional-resources"></a>Recursos adicionales

* [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica la implementación de aplicaciones web y sitios web en servidores de IIS.
* [Repositorio de SDK web en GitHub](https://github.com/dotnet/websdk/issues): problemas de archivos y características de solicitud para la implementación.
* [Publicación de una aplicación web ASP.NET en una máquina virtual de Azure desde Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
