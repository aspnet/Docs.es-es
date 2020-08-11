---
title: Implementar aplicaciones de ASP.NET Core en Azure App Service
author: bradygaster
description: Este artículo contiene vínculos a recursos de implementación y hospedaje de Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 11de6b04f6813161e5eaee294f3e67e223ae0db3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015925"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a>Implementar aplicaciones de ASP.NET Core en Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/) es un [servicio de plataforma de informática en la nube de Microsoft](https://azure.microsoft.com/) que sirve para hospedar aplicaciones web, como ASP.NET Core.

## <a name="useful-resources"></a>Recursos útiles

La [documentación de App Service](/azure/app-service/) es un recurso que incluye documentación, tutoriales, ejemplos, guías de procedimientos y otros recursos de aplicaciones de Azure. Dos tutoriales importantes que pertenecen al hospedaje de aplicaciones de ASP.NET Core son:

[Creación de una aplicación web ASP.NET Core en Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Usar Visual Studio para crear e implementar una aplicación web de ASP.NET Core para Azure App Service en Windows.

[Creación de una aplicación ASP.NET Core en App Service en Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Usar la línea de comandos para crear e implementar una aplicación web de ASP.NET Core para Azure App Service en Linux.

Consulte el [panel ASP.NET Core en App Service](https://aspnetcoreon.azurewebsites.net/) para ver la versión de ASP.NET Core disponible en Azure App Service.

Suscríbase al repositorio [App Service Announcements](https://github.com/Azure/app-service-announcements/) y supervise los problemas. El equipo de App Service publica periódicamente los anuncios y escenarios que llegan a App Service.

Los artículos siguientes están disponibles en la documentación de ASP.NET Core:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Obtenga información sobre cómo publicar una aplicación de ASP.NET Core en Azure App Service con Visual Studio.

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
Obtenga información sobre cómo crear una aplicación web de ASP.NET Core con Visual Studio e implementarla en Azure App Service con Git para una implementación continua.

[Creación de la primera canalización](/azure/devops/pipelines/get-started-yaml)  
Configure una compilación de integración continua para una aplicación de ASP.NET Core y, después, cree una versión de implementación continua para Azure App Service.

[Espacio aislado de Azure Web App](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Detecte limitaciones de ejecución en tiempo de ejecución de Azure App Service aplicadas por la plataforma de aplicaciones de Azure.

<xref:test/troubleshoot>  
Conozca y solucione advertencias y errores en proyectos de ASP.NET Core.

## <a name="application-configuration"></a>Configuración de aplicación

### <a name="platform"></a>Plataforma

La arquitectura de plataforma (x86/x64) de una aplicación de App Services se establece en la configuración de la aplicación de Azure Portal para las aplicaciones hospedadas en un cálculo de serie A (básico) o en un nivel de hospedaje superior. Confirme que la configuración de publicación de la aplicación (por ejemplo, en el [perfil de publicación [.pubxml])](xref:host-and-deploy/visual-studio-publish-profiles) de Visual Studio coincide con los valores de configuración de servicio de la aplicación en Azure Portal.

::: moniker range=">= aspnetcore-2.2"

Los runtimes para aplicaciones de 64 bits (x64) y 32 bits (x86) están presentes en Azure App Service. El [SDK de .NET Core](/dotnet/core/sdk) que está disponible en App Service es para 32 bits, pero es posible implementar aplicaciones de 64 bits compiladas localmente con la consola de [Kudu](https://github.com/projectkudu/kudu/wiki) o el proceso de publicación de Visual Studio. Para obtener más información, consulte la sección [Publicar e implementar la aplicación](#publish-and-deploy-the-app).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

En el caso de las aplicaciones con dependencias nativas, los runtimes de 32 bits (x86) están presentes Azure App Service. El [SDK de .NET Core](/dotnet/core/sdk) disponible en App Service es de 32 bits.

::: moniker-end

Para obtener más detalles sobre los componentes y los métodos de distribución del marco .NET Core —por ejemplo, información sobre .NET Core Runtime—, consulte [Sobre .NET Core: Composición](/dotnet/core/about#composition).

### <a name="packages"></a>Paquetes

Incluya los siguientes paquetes de NuGet para proporcionar características de registro automáticas para aplicaciones implementadas en Azure App Service:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) usa [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para proporcionar a ASP.NET Core una integración ligera (Light-Up) con Azure App Service. El paquete `Microsoft.AspNetCore.AzureAppServicesIntegration` proporciona las características de registro agregadas.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) ejecuta [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) para agregar a Azure App Service proveedores de registro de diagnósticos en el paquete `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) proporciona implementaciones de registrador para admitir registros de diagnósticos de Azure App Service y características de transmisión en secuencias de registro.

Los paquetes anteriores no están disponibles en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Las aplicaciones que tienen como destino .NET Framework o hacen referencia al metapaquete `Microsoft.AspNetCore.App` deben hacer referencia de forma explícita a los paquetes individuales del archivo de proyecto de la aplicación.

## <a name="override-app-configuration-using-the-azure-portal"></a>Invalidación de la configuración de la aplicación mediante Azure Portal

::: moniker range=">= aspnetcore-3.0"

La configuración de la aplicación en Azure Portal le permite establecer variables de entorno para la aplicación. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables) puede consumir las variables de entorno.

Cuando una configuración de aplicación se crea o modifica en Azure Portal y el botón **Guardar** está seleccionado, se reinicia la aplicación de Azure. La variable de entorno está disponible para la aplicación después de que se reinicie el servicio.

Cuando una aplicación usa el [host genérico](xref:fundamentals/host/generic-host), las variables de entorno se cargan en la configuración de la aplicación cuando se llama a <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> para compilar el host. Para más información, consulte <xref:fundamentals/host/generic-host> y el [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables).

::: moniker-end
::: moniker range="< aspnetcore-3.0"

La configuración de la aplicación en Azure Portal le permite establecer variables de entorno para la aplicación. El [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider) puede consumir las variables de entorno.

Cuando una configuración de aplicación se crea o modifica en Azure Portal y el botón **Guardar** está seleccionado, se reinicia la aplicación de Azure. La variable de entorno está disponible para la aplicación después de que se reinicie el servicio.

Cuando una aplicación usa el [host web](xref:fundamentals/host/web-host), las variables de entorno se cargan en la configuración de la aplicación cuando se llama a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para compilar el host. Para más información, consulte <xref:fundamentals/host/web-host> y el [proveedor de configuración de variables de entorno](xref:fundamentals/configuration/index#environment-variables-configuration-provider).

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Escenarios de servidor proxy y equilibrador de carga

El [middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), que configura el software intermedio de encabezados reenviados al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), y el módulo de ASP.NET Core están configurados para reenviar el esquema (HTTP/HTTPS) y la dirección IP remota donde se originó la solicitud. Podría ser necesario realizar una configuración adicional para las aplicaciones hospedadas detrás de servidores proxy y equilibradores de carga adicionales. Para más información, vea [Configurar ASP.NET Core para trabajar con servidores proxy y equilibradores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Supervisión y registro

::: moniker range=">= aspnetcore-3.0"

Las aplicaciones ASP.NET Core implementadas de forma automática en App Service reciben una extensión de App Service, **Integración de registro de ASP.NET Core**. La extensión habilita la integración de registro para las aplicaciones ASP.NET Core en Azure App Service.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Las aplicaciones de ASP.NET Core implementadas automáticamente en App Service reciben una extensión de App Service, **Extensiones de registro de ASP.NET Core**. La extensión habilita la integración de registro para las aplicaciones ASP.NET Core en Azure App Service.

::: moniker-end

Para obtener información sobre supervisión, registro y solución de problemas, consulte los artículos siguientes:

[Supervisar aplicaciones en Azure App Service](/azure/app-service/web-sites-monitor)  
Obtenga información sobre cómo revisar las cuotas y las métricas para las aplicaciones y los planes de App Service.

[Habilitar el registro de diagnósticos para las aplicaciones en Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)  
Descubra cómo habilitar y acceder a registro de diagnóstico para los códigos de estado HTTP, solicitudes con error y actividad del servidor web.

<xref:fundamentals/error-handling>  
Conozca los métodos habituales para controlar los errores en las aplicaciones de ASP.NET Core.

<xref:test/troubleshoot-azure-iis>  
Obtenga información sobre cómo diagnosticar problemas con las implementaciones de Azure App Service con las aplicaciones de ASP.NET Core.

<xref:host-and-deploy/azure-iis-errors-reference>  
Consulte los errores comunes de configuración de implementación para las aplicaciones hospedadas por Azure App Service/IIS con consejos de solución de problemas.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Anillo de clave de protección de datos y ranuras de implementación

Las [claves de protección de datos](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) se conservan en la carpeta *%HOME%\ASP.NET\DataProtection-Keys*. Esta carpeta está respaldada por el almacenamiento de red y se sincroniza en todas las máquinas que hospedan la aplicación. Las claves no están protegidas en reposo. Esta carpeta proporciona el anillo de clave a todas las instancias de una aplicación en una única ranura de implementación. Las ranuras de implementación independientes, por ejemplo, almacenamiento provisional y producción, no comparten ningún anillo de clave.

Al realizar un intercambio entre ranuras de implementación, cualquier sistema que utilice la protección de datos no podrá descifrar los datos almacenados mediante el anillo de clave situado dentro de la ranura anterior. El middleware de Cookie de ASP.NET usa protección de datos para proteger sus cookies. Esto hace que los usuarios cierren sesión en una aplicación que usa el middleware de Cookie de ASP.NET estándar. Para una solución de anillo de clave independiente de la ranura, utilice un proveedor de anillo de clave externo, como estos:

* Azure Blob Storage
* Azure Key Vault
* Almacén SQL
* Redis Cache

Para obtener más información, vea <xref:security/data-protection/implementation/key-storage-providers>.
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a>Implementación de una aplicación ASP.NET Core que usa una versión preliminar de .NET Core

Para implementar una aplicación que usa una versión preliminar de .NET Core, consulte los siguientes recursos. Estos enfoques también se usan cuando el entorno de ejecución está disponible pero el SDK no se ha instalado en Azure App Service.

* [Especificación del SDK de .NET Core con Azure Pipelines](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [Implementación de la versión preliminar de una aplicación independiente](#deploy-a-self-contained-preview-app)
* [Usar Docker con Web Apps para contenedores](#use-docker-with-web-apps-for-containers)
* [Instalación de la extensión de sitio de versión preliminar](#install-the-preview-site-extension)

Consulte el [panel ASP.NET Core en App Service](https://aspnetcoreon.azurewebsites.net/) para ver la versión de ASP.NET Core disponible en Azure App Service.

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a>Especificación de la versión del SDK de .NET Core con Azure Pipelines

Use los [escenarios de CI/CD de Azure App Service](/azure/app-service/deploy-continuous-deployment) para configurar una compilación de integración continua con Azure DevOps. Después de crear la compilación de Azure DevOps, tiene la opción de configurar la compilación para usar una versión específica del SDK. 

#### <a name="specify-the-net-core-sdk-version"></a>Especificación de la versión del SDK de .NET Core

Al usar el centro de implementación de App Service para crear una compilación de Azure DevOps, la canalización de compilación predeterminada incluye los pasos `Restore`, `Build`, `Test` y `Publish`. Para especificar la versión del SDK, seleccione el botón **Add (+)** (Agregar [+]) en la lista de trabajos del agente para agregar un nuevo paso. Busque **.NET Core SDK** (SDK de .NET Core) en la barra de búsqueda. 

![Paso para agregar el SDK de .NET Core](index/add-sdk-step.png)

Mueva el paso a la primera posición de la compilación para que los pasos siguientes usen la versión especificada del SDK de .NET Core. Especifique la versión del SDK de .NET Core. En este ejemplo, el SDK se establece en `3.0.100`.

![Paso del SDK completado](index/sdk-step-first-place.png)

Para publicar una [implementación independiente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configúrela en el paso `Publish` y proporcione el [identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog).

![Publicación independiente](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a>Implementación de la versión preliminar de una aplicación independiente

Una [implementación independiente (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) que tiene como destino un entorno de ejecución en versión preliminar transporta el entorno de ejecución en versión preliminar en la implementación.

Al implementar una aplicación independiente:

* El sitio en Azure App Service no requiere la [extensión del sitio en versión preliminar](#install-the-preview-site-extension).
* Se debe publicar la aplicación siguiendo un enfoque diferente que cuando se publica para una [implementación dependiente del marco (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).

Siga las instrucciones de la sección [Implementación de la aplicación independiente](#deploy-the-app-self-contained).

### <a name="use-docker-with-web-apps-for-containers"></a>Usar Docker con Web Apps para contenedores

[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contiene las imágenes de Docker de versión preliminar más recientes. Las imágenes se pueden usar como base. Use la imagen y efectúe la implementación en Web App for Containers con normalidad.

### <a name="install-the-preview-site-extension"></a>Instalación de la extensión de sitio de versión preliminar

Si tiene algún problema al usar la extensión de sitio de versión preliminar, abra una [incidencia de dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).

1. En Azure Portal, vaya a App Service.
1. Seleccione la aplicación web.
1. Escriba "ex" en el cuadro de búsqueda para filtrar por "Extensiones" o desplácese hacia abajo en la lista de herramientas de administración.
1. Seleccione **Extensiones**.
1. Seleccione **Agregar**.
1. Seleccione la extensión **Runtime de ASP.NET Core {X.Y} ({x64|x86})** en la lista, en la que `{X.Y}` es la versión preliminar de ASP.NET Core y `{x64|x86}` especifica la plataforma.
1. Seleccione **Aceptar** para aceptar los términos legales.
1. Seleccione **Aceptar** para instalar la extensión.

Cuando se complete la operación, se instalará la versión preliminar de .NET Core. Compruebe la instalación:

1. Seleccione **Herramientas avanzadas**.
1. Seleccione **Ir** en **Herramientas avanzadas**.
1. Seleccione el elemento de menú **Consola de depuración** > **PowerShell**.
1. Ejecute el siguiente comando en el símbolo del sistema de PowerShell: Sustituya la versión de runtime de ASP.NET Core por `{X.Y}` y la plataforma por `{PLATFORM}` en el comando:

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   El comando devuelve `True` cuando está instalado el runtime de la versión preliminar de x64.

> [!NOTE]
> La arquitectura de plataforma (x86/x64) de una aplicación de App Services se establece en la configuración de la aplicación de Azure Portal para las aplicaciones hospedadas en un cálculo de serie A (básico) o en un nivel de hospedaje superior. Confirme que la configuración de publicación de la aplicación (por ejemplo, en el [perfil de publicación [.pubxml])](xref:host-and-deploy/visual-studio-publish-profiles) de Visual Studio coincide con los valores de configuración de servicio de la aplicación en Azure Portal.
>
> Si la aplicación se ejecuta en modo de en proceso y la arquitectura de plataforma está configurada para 64 bits (x64), el módulo ASP.NET Core usa el runtime de la versión preliminar de 64 bits, si está presente. Instale la extensión del **entorno de ejecución de ASP.NET Core {X.Y} (x64)** mediante Azure Portal.
>
> Después de instalar el entorno de ejecución de la versión preliminar de x64, ejecute el siguiente comando en la ventana de comandos de Kudu PowerShell de Azure para comprobar la instalación. Sustituya la versión del entorno de ejecución de ASP.NET Core por `{X.Y}` en el comando siguiente:
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> El comando devuelve `True` cuando está instalado el runtime de la versión preliminar de x64.

> [!NOTE]
> Con **Extensiones de ASP.NET Core** se obtienen funciones adicionales para ASP.NET Core en Azure App Services, como habilitar el registro de Azure. La extensión se instala automáticamente cuando se implementa desde Visual Studio. Si no se instala la extensión, instálela para la aplicación.

**Uso de la extensión de sitio de versión preliminar con una plantilla de ARM**

Si usa una plantilla de ARM para crear e implementar aplicaciones, puede usar el tipo de recurso `siteextensions` para agregar la extensión de sitio a una aplicación web. Por ejemplo:

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a>Publicar e implementar la aplicación

::: moniker range=">= aspnetcore-2.2"

Para una implementación de 64 bits:

* Use un SDK de .NET Core de 64 bits para compilar una aplicación de 64 bits.
* Establezca la **Plataforma** en **64 bits** en **Configuración** > **Configuración general** de App Service. La aplicación debe usar un plan de servicio básico o superior para habilitar la elección del valor de bits de la plataforma.

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a>Implementación de la aplicación dependiente del marco de trabajo

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Seleccione **Compilar** > **Publicar {Nombre de la aplicación}** en la barra de herramientas de Visual Studio o, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.
1. En el cuadro de diálogo **Elegir un destino de publicación**, confirme que está seleccionado **App Service**.
1. Seleccione **Opciones avanzadas**. Se abre el cuadro de diálogo **Publicar**.
1. En el cuadro de diálogo **Publicar**:
   * Confirme que está seleccionada la configuración de **Versión**.
   * Abra la lista desplegable **Modo de implementación** y seleccione **Dependiente de marco de trabajo**.
   * Seleccione **Portátil** como **Tiempo de ejecución de destino**.
   * Si necesita quitar archivos adicionales tras la implementación, abra **Opciones de publicación de archivos** y active la casilla de verificación para quitar archivos adicionales en el destino.
   * Seleccione **Guardar**.
1. Cree un sitio nuevo o actualice un sitio existente siguiendo las instrucciones restantes del asistente para publicación.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En el archivo de proyecto, no especifique [Identificador en tiempo de ejecución (RID)](/dotnet/core/rid-catalog).

1. Desde un shell de comandos, publique la aplicación en Configuración de versión con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish). En el ejemplo siguiente, la aplicación está publicada como aplicación dependiente de marco de trabajo:

   ```console
   dotnet publish --configuration Release
   ```

1. Mueva el contenido del directorio *bin/Release/{TARGET FRAMEWORK}/publish* al sitio de App Service. Si va a arrastrar el contenido de la carpeta *publish* de su disco duro local o de un recurso compartido de red directamente a App Service en la consola de [Kudu](https://github.com/projectkudu/kudu/wiki), arrastre los archivos a la carpeta `D:\home\site\wwwroot` en la consola de Kudu.

---

### <a name="deploy-the-app-self-contained"></a>Implementación de la aplicación independiente

Use Visual Studio o la CLI de .NET Core para una [implementación autocontenida (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Seleccione **Compilar** > **Publicar {Nombre de la aplicación}** en la barra de herramientas de Visual Studio o, en el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.
1. En el cuadro de diálogo **Elegir un destino de publicación**, confirme que está seleccionado **App Service**.
1. Seleccione **Opciones avanzadas**. Se abre el cuadro de diálogo **Publicar**.
1. En el cuadro de diálogo **Publicar**:
   * Confirme que está seleccionada la configuración de **Versión**.
   * Abra la lista desplegable **Modo de implementación** y seleccione **Independiente**.
   * Seleccione el entorno de ejecución de destino en la lista desplegable **Tiempo de ejecución de destino**. De manera predeterminada, es `win-x86`.
   * Si necesita quitar archivos adicionales tras la implementación, abra **Opciones de publicación de archivos** y active la casilla de verificación para quitar archivos adicionales en el destino.
   * Seleccione **Guardar**.
1. Cree un sitio nuevo o actualice un sitio existente siguiendo las instrucciones restantes del asistente para publicación.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

1. En el archivo de proyecto, especifique uno o más [identificadores de entorno de ejecución (RID)](/dotnet/core/rid-catalog). Use `<RuntimeIdentifier>` (en singular) para un único RID o use `<RuntimeIdentifiers>` (en plural) para proporcionar una lista de RID delimitada por puntos y coma. En el ejemplo siguiente, se especifica el RID `win-x86`:

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. Desde un shell de comandos, publique la aplicación en la configuración de la versión para el entorno de ejecución del host con el comando [dotnet publish](/dotnet/core/tools/dotnet-publish). En el ejemplo siguiente, la aplicación está publicada para el RID `win-x86`. El RID proporcionado para la opción `--runtime` debe indicarse en la propiedad `<RuntimeIdentifier>` (o `<RuntimeIdentifiers>`) del archivo de proyecto.

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. Mueva el contenido del directorio *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* al sitio de App Service. Si va a arrastrar el contenido de la carpeta *publish* de su disco duro local o de un recurso compartido de red directamente a App Service en la consola de Kudu, arrastre los archivos a la carpeta `D:\home\site\wwwroot` en la consola de Kudu.

---

## <a name="protocol-settings-https"></a>Configuración del protocolo (HTTPS)

Los enlaces de protocolo seguro permiten especificar un certificado para usarlo al responder a solicitudes a través de HTTPS. Los enlaces requieren un certificado privado válido ( *.pfx*) que se haya emitido para el nombre de host en cuestión. Para obtener más información, consulte [Tutorial: Enlazar un certificado SSL personalizado existente a Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).

## <a name="transform-webconfig"></a>Transformación de web.config

Si necesita transformar *web.config* al realizar la publicación (por ejemplo, establecer variables de entorno basadas en la configuración, el perfil o el entorno), consulte <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="additional-resources"></a>Recursos adicionales

* [Información general de App Service](/azure/app-service/app-service-web-overview)
* [Azure App Service: el mejor lugar para hospedar las aplicaciones .NET (vídeo de introducción de 55 minutos)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure Friday: experiencia de diagnóstico y solución de problemas de Azure App Service (vídeo de 12 minutos)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Introducción a diagnósticos de Azure App Service](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Azure App Service en Windows Server utiliza [Internet Information Services (IIS)](https://www.iis.net/). Los temas siguientes se aplican a la tecnología subyacente de IIS:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Windows Server: contenido de administradores de TI para versiones anteriores y actuales](/windows-server/windows-server-versions)
