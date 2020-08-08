---
title: Aprenda a migrar de ASP.NET MVC a ASP.NET Core MVC
author: wadepickett
description: Obtenga información sobre cómo iniciar la migración de un proyecto de ASP.NET MVC a ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 17f2a2532c58c3796835328260231d63f8fb2e40
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015054"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migración de ASP.NET MVC a ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core MVC](xref:mvc/overview). En el proceso, se resaltan los cambios relacionados de ASP.NET MVC.

La migración de ASP.NET MVC es un proceso de varios pasos. En este artículo se describe:

* Instalación inicial.
* Controladores y vistas básicos.
* Contenido estático.
* Dependencias del lado cliente.

Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Crear el proyecto de MVC ASP.NET MVC

Cree un proyecto de ASP.NET MVC de ejemplo en Visual Studio para migrar:

1. En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.
1. Seleccione **aplicación Web de ASP.net (.NET Framework)** y, a continuación, seleccione **siguiente**.
1. Asigne al proyecto el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente. Seleccione **Crear**.
1. Seleccione **MVC**y, a continuación, seleccione **crear**.

## <a name="create-the-aspnet-core-project"></a>Crear el proyecto de ASP.NET Core

Cree una nueva solución con un nuevo proyecto de ASP.NET Core al que migrar:

1. Inicie una segunda instancia de Visual Studio.
1. En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.
1. Seleccione **ASP.net web Core Web Application** y, a continuación, seleccione **siguiente**.
1. En el cuadro de diálogo **configurar el nuevo proyecto** , asigne al proyecto el nombre *WebApp1*.
1. Establezca la ubicación en un directorio diferente al proyecto anterior para usar el mismo nombre de proyecto. El uso del mismo espacio de nombres facilita la copia de código entre los dos proyectos. Seleccione **Crear**.
1. En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas. Seleccione la plantilla de proyecto **aplicación web (controlador de vista de modelos)** y seleccione **crear**.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Configuración del sitio de ASP.NET Core para usar MVC

En ASP.NET Core proyectos de 3,0 y versiones posteriores, .NET Framework ya no es una plataforma de destino admitida. El proyecto debe tener como destino .NET Core. La ASP.NET Core marco de trabajo compartido, que incluye MVC, forma parte de la instalación del entorno de ejecución de .NET Core. Se hace referencia al marco compartido automáticamente al usar el SDK `Microsoft.NET.Sdk.Web` en el archivo de proyecto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Para obtener más información, vea [referencia de Framework](xref:migration/22-to-30#framework-reference).

En ASP.NET Core, la `Startup` clase:

* Reemplaza a *global. asax*.
* Controla todas las tareas de inicio de la aplicación.

Para obtener más información, vea <xref:fundamentals/startup>.

En el proyecto de ASP.NET Core, abra el archivo *Startup.CS* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

ASP.NET Core aplicaciones deben participar en las características de .NET Framework con middleware. El código generado anteriormente por la plantilla agrega los siguientes servicios y middleware:

* El <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> método de extensión registra la compatibilidad del servicio MVC con los controladores, las características relacionadas con la API y las vistas. Para obtener más información sobre las opciones de registro del servicio MVC, consulte [registro del servicio MVC](xref:migration/22-to-30#mvc-service-registration)
* El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método de extensión agrega el controlador de archivos estáticos `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`Se debe llamar antes al método de extensión `UseRouting` . Para obtener más información, vea <xref:fundamentals/static-files>.
* El <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> método de extensión agrega enrutamiento. Para obtener más información, vea <xref:fundamentals/routing>.

Esta configuración existente incluye lo que se necesita para migrar el proyecto de ejemplo ASP.NET MVC. Para obtener más información acerca de ASP.NET Core opciones de middleware, vea <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrar controladores y vistas

En el proyecto de ASP.NET Core, se agregaría una nueva clase de controlador vacía y una clase de vista para servir como marcadores de posición usando los mismos nombres que las clases de controlador y de vista de cualquier proyecto de ASP.NET MVC del que migrar.

El proyecto de ASP.NET Core *WebApp1* ya incluye un controlador de ejemplo mínimo y una vista con el mismo nombre que el proyecto de MVC de ASP.net. Por lo tanto, estos actuarán como marcadores de posición para el controlador ASP.NET MVC y las vistas que se van a migrar desde el proyecto de ASP.NET MVC *WebApp1* .

1. Copie los métodos de MVC de ASP.NET `HomeController` para reemplazar los nuevos métodos de ASP.net Core `HomeController` . No es necesario cambiar el tipo de valor devuelto de los métodos de acción. El tipo de valor devuelto del método de acción del controlador de la plantilla integrada de ASP.NET MVC es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar. `ActionResult` implementa `IActionResult`.
1. En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Home* y seleccione **Agregar** > **elemento existente**.
1. En el cuadro de diálogo **Add Existing Item (Agregar elemento existente** ), desplácese hasta el directorio de *inicios/* del proyecto de ASP.NET MVC *WebApp1* .
1. Seleccione los archivos *de vista about. cshtml*, *Contact. cshtml*e *index. cshtml* y, Razor a continuación, seleccione **Agregar**y reemplace los archivos existentes.

Para obtener más información, vea <xref:mvc/controllers/actions> y <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Prueba de cada método

Los puntos de conexión de cada controlador se pueden probar, sin embargo, el diseño y los estilos se describen más adelante en el documento.

1. Ejecute la aplicación ASP.NET Core.
1. Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core. Por ejemplo: `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrar contenido estático

En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en el directorio raíz del proyecto web y se combinaba con los archivos del lado servidor. En ASP.NET Core, los archivos estáticos se almacenan en el directorio [raíz Web](xref:fundamentals/index#web-root) del proyecto. El directorio predeterminado es *{Content root}/wwwroot*, pero se puede cambiar. Para obtener más información, vea [Archivos estáticos en ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Copie el contenido estático del proyecto de ASP.NET MVC *WebApp1* en el directorio *wwwroot* del proyecto ASP.net Core *WebApp1* :

1. En el proyecto de ASP.net Core, haga clic con el botón secundario en el directorio *wwwroot* y seleccione **Agregar** > **elemento existente**.
1. En el cuadro de diálogo **Agregar elemento existente** , vaya al proyecto de ASP.NET MVC *WebApp1* .
1. Seleccione el archivo *favicon. ico* y, a continuación, seleccione **Agregar**, reemplazando el archivo existente.

## <a name="migrate-the-layout-files"></a>Migrar los archivos de diseño

Copie los archivos de diseño del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:

1. En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views* y seleccione **Agregar** > **elemento existente**.
1. En el cuadro de diálogo **Agregar elemento existente** , vaya al directorio de *vistas* del proyecto de ASP.NET MVC *WebApp1* .
1. Seleccione el archivo *_ViewStart. cshtml* y, después, seleccione **Agregar**.

Copie los archivos de diseño compartidos del proyecto de ASP.NET MVC en el proyecto ASP.NET Core:

1. En el proyecto de ASP.net Core, haga clic con el botón derecho en el directorio *views/Shared* y seleccione **Agregar** > **elemento existente**.
1. En el cuadro de diálogo **Agregar elemento existente** , desplácese hasta el directorio *compartido o vistas* del proyecto de ASP.NET MVC *WebApp1* .
1. Seleccione el archivo *_Layout. cshtml* , seleccione **Agregar**y reemplace el archivo existente.

En el proyecto de ASP.NET Core, abra el archivo *_Layout. cshtml* . Realice los siguientes cambios para que coincidan con el código completado que se muestra a continuación:

Actualice la inclusión de CSS de bootstrap para que coincida con el código completado a continuación:

1. Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).
1. Quite `@Scripts.Render("~/bundles/modernizr")`.

El marcado de reemplazo completado para la inclusión de CSS de bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Actualice la inclusión de jQuery y bootstrap de JavaScript para que coincida con el código completado a continuación:

1. Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).
1. Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).

El marcado de reemplazo completado para jQuery y bootstrap script de inclusión:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

El archivo *_Layout. cshtml* actualizado se muestra a continuación:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Vea el sitio en el explorador. Debe representarse con los estilos esperados en su lugar.

## <a name="configure-bundling-and-minification"></a>Configuración de la unión y la minimización

ASP.NET Core es compatible con varias soluciones minificación y de empaquetado de código abierto, como [Weboptimizer](https://github.com/ligershark/WebOptimizer) y otras bibliotecas similares. ASP.NET Core no proporciona una solución minificación y de Unión nativa. Para obtener información sobre la configuración de la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Solucionar errores HTTP 500

Hay muchos problemas que pueden provocar un mensaje de error HTTP 500 que no contiene información sobre el origen del problema. Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500. De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando el entorno se está *desarrollando*. Esto se detalla en el código siguiente:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500. Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor. Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Pasos siguientes

* <xref:migration/identity>

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,2. En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC. La migración de ASP.NET MVC es un proceso de varios pasos. En este artículo se describe:

* Instalación inicial
* Controladores y vistas básicos
* Contenido estático
* Dependencias del lado cliente.

Para migrar la configuración y el Identity código, vea <xref:migration/configuration> y <xref:migration/identity> .

> [!NOTE]
> Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.

## <a name="create-the-starter-aspnet-mvc-project"></a>Crear el proyecto de MVC ASP.NET MVC

Para demostrar la actualización, comenzaremos por crear una aplicación ASP.NET MVC. Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*. Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.

## <a name="create-the-aspnet-core-project"></a>Crear el proyecto de ASP.NET Core

Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos. Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos. Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* . Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**. Cambie el nombre de esta aplicación a *FullAspNetCore*. Al crear este proyecto, se ahorra tiempo en la conversión. El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.

## <a name="configure-the-site-to-use-mvc"></a>Configurar el sitio para usar MVC

* Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada. Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC. Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.

`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático. ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos. Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).

* Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos. Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Agregar un controlador y una vista

En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.

* Agregue un directorio de *Controladores* .

* Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* Agregue un directorio *views* .

* Agregue un directorio de *vistas o inicio* .

* Agregue una ** Razor vista** denominada *index. cshtml* al directorio *views/Home* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

La estructura del proyecto se muestra a continuación:

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:

```html
<h1>Hello world!</h1>
```

Ejecute la aplicación.

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).

La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:

* contenido del lado cliente (CSS, fuentes y scripts)

* controllers

* views

* modelos

* Unión

* filters

* Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).

## <a name="controllers-and-views"></a>Controladores y vistas

* Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` . En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar. `ActionResult`implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.

* Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.

## <a name="test-each-method"></a>Prueba de cada método

Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista. Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.

Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core. Por ejemplo: `https://localhost:44375/home/about`.

![Página de contacto](mvc/_static/contact-page.png)

Tenga en cuenta la falta de estilo y elementos de menú. El estilo se corregirá en la sección siguiente.

## <a name="static-content"></a>Contenido estático

En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor. En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* . Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core. En esta conversión de ejemplo:

* Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.

El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* . La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*). Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto. En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.

## <a name="migrate-the-layout-file"></a>Migrar el archivo de diseño

* Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core. El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.

* Cree un directorio *views/Shared* .

* *Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core. Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* . El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro). Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño. El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.

* Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.

Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):

* Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).

* Quite `@Scripts.Render("~/bundles/modernizr")`.

* Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ). Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)

* Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).

* Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).

El marcado de reemplazo para la inclusión de CSS de bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

El archivo *_Layout. cshtml* actualizado se muestra a continuación:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Vea el sitio en el explorador. Ahora debería cargarse correctamente, con los estilos esperados en su lugar.

* *Opcional:* Pruebe a usar el nuevo archivo de diseño. Copie el archivo de diseño del proyecto *FullAspNetCore* . El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.

## <a name="configure-bundling-and-minification"></a>Configuración de la unión y la minimización

Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Solucionar errores HTTP 500

Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema. Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500. De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*. Vea un ejemplo en el código siguiente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500. Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor. Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

En este artículo se muestra cómo iniciar la migración de un proyecto de ASP.NET MVC a [ASP.net Core mvc](xref:mvc/overview) 2,1. En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC. La migración de ASP.NET MVC es un proceso de varios pasos. En este artículo se describe:

* Instalación inicial
* Controladores y vistas básicos
* Contenido estático
* Dependencias del lado cliente.

Para migrar la configuración y el Identity código, vea [migrar la configuración a ASP.net Core](xref:migration/configuration) y [migrar la autenticación y Identity a ASP.net Core](xref:migration/identity).

> [!NOTE]
> Es posible que los números de versión de los ejemplos no estén actualizados, actualice los proyectos en consecuencia.

## <a name="create-the-starter-aspnet-mvc-project"></a>Crear el proyecto de MVC ASP.NET MVC

Para demostrar la actualización, comenzaremos por crear una aplicación de ASP.NET MVC. Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el proyecto de ASP.net Core creado en el paso siguiente.

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*. Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.

## <a name="create-the-aspnet-core-project"></a>Crear el proyecto de ASP.NET Core

Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos. Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos. Cree este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* . Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**. Cambie el nombre de esta aplicación a *FullAspNetCore*. Al crear este proyecto, se ahorra tiempo en la conversión. El resultado final se puede ver en el código generado por la plantilla, el código puede copiarse en el proyecto de conversión o compararse con el proyecto generado por una plantilla.

## <a name="configure-the-site-to-use-mvc"></a>Configurar el sitio para usar MVC

* Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada. Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC. Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.

`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core. `Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático. ASP.NET Core aplicaciones optan explícitamente a un middleware, como para servir archivos estáticos. Para obtener más información, consulte [Archivos estáticos](xref:fundamentals/static-files).

* Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

El <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> método de extensión agrega el controlador de archivos estáticos. El `UseMvc` método de extensión agrega enrutamiento. Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Agregar un controlador y una vista

En esta sección, se agrega un controlador y una vista mínimos para servir como marcadores de posición para el controlador ASP.NET MVC y las vistas migradas en la sección siguiente.

* Agregue un directorio de *Controladores* .

* Agregue una **clase de controlador** denominada *HomeController.CS* al directorio *Controllers* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* Agregue un directorio *views* .

* Agregue un directorio de *vistas o inicio* .

* Agregue una ** Razor vista** denominada *index. cshtml* al directorio *views/Home* .

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

La estructura del proyecto se muestra a continuación:

![Explorador de soluciones que muestra los archivos y directorios de WebApp1](mvc/_static/project-structure-controller-view.png)

Reemplace el contenido del archivo *Views/Home/Index.cshtml* por el marcado siguiente:

```html
<h1>Hello world!</h1>
```

Ejecute la aplicación.

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

Para obtener más información, vea [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview).

La funcionalidad siguiente requiere la migración del proyecto de ejemplo ASP.NET MVC al proyecto ASP.NET Core:

* contenido del lado cliente (CSS, fuentes y scripts)

* controllers

* views

* modelos

* Unión

* filters

* Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).

## <a name="controllers-and-views"></a>Controladores y vistas

* Copie cada uno de los métodos de MVC de ASP.NET `HomeController` al nuevo `HomeController` . En ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción devuelven en `IActionResult` su lugar. `ActionResult`implementa `IActionResult` , por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.

* Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *Index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.

## <a name="test-each-method"></a>Prueba de cada método

Todavía no se han migrado el archivo de diseño y los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista. Los vínculos generados por el archivo de diseño para las `About` `Contact` vistas y no estarán disponibles todavía.

* Invoque las vistas representadas desde el explorador en la aplicación ASP.NET Core en ejecución reemplazando el número de puerto actual por el número de Puerto usado en el proyecto de ASP.NET Core. Por ejemplo: `https://localhost:44375/home/about`.

![Página de contacto](mvc/_static/contact-page.png)

Tenga en cuenta la falta de estilo y elementos de menú. El estilo se corregirá en la sección siguiente.

## <a name="static-content"></a>Contenido estático

En ASP.NET MVC 5 y versiones anteriores, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor. En ASP.NET Core, el contenido estático se hospeda en el directorio *wwwroot* . Copie el contenido estático de la aplicación ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core. En esta conversión de ejemplo:

* Copie el archivo *favicon. ico* del proyecto ASP.NET MVC en el directorio *wwwroot* del proyecto ASP.net Core.

El proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en los directorios de *contenido* y *scripts* . La plantilla, que generó el proyecto ASP.NET MVC, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*). Los archivos *bootstrap.js* y *bootstrap. CSS* se pueden copiar del proyecto ASP.NET MVC al directorio *wwwroot* del nuevo proyecto. En su lugar, este documento agrega compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN, en la sección siguiente.

## <a name="migrate-the-layout-file"></a>Migrar el archivo de diseño

* Copie el archivo *_ViewStart. cshtml* del directorio *views* del proyecto ASP.NET MVC en el directorio *views* del proyecto ASP.net Core. El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.

* Cree un directorio *views/Shared* .

* *Opcional:* Copie *_ViewImports. cshtml* del directorio *views* del proyecto de *FullAspNetCore* MVC en el directorio *views* del proyecto ASP.net Core. Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* . El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro). Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño. El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.

* Copie el archivo *_Layout. cshtml* del directorio de *vistas/compartidos* del proyecto de ASP.NET MVC en el directorio de *vistas/compartidos* del proyecto de ASP.net Core.

Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):

* Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).

* Quite `@Scripts.Render("~/bundles/modernizr")`.

* Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@` ). Para obtener más información, vea [migrar la autenticación y Identity ASP.net Core](xref:migration/identity)

* Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).

* Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).

El marcado de reemplazo para la inclusión de CSS de bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

El archivo *_Layout. cshtml* actualizado se muestra a continuación:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Vea el sitio en el explorador. Ahora debería cargarse correctamente, con los estilos esperados en su lugar.

* *Opcional:* Pruebe a usar el nuevo archivo de diseño. Copie el archivo de diseño del proyecto *FullAspNetCore* . El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.

## <a name="configure-bundling-and-minification"></a>Configuración de la unión y la minimización

Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Solucionar errores HTTP 500

Hay muchos problemas que pueden producir mensajes de error HTTP 500 que no contienen información sobre el origen del problema. Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, se genera un error http 500. De forma predeterminada, en ASP.NET Core aplicaciones, la `UseDeveloperExceptionPage` extensión se agrega a `IApplicationBuilder` y se ejecuta cuando la configuración es *desarrollo*. Vea un ejemplo en el código siguiente:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core convierte las excepciones no controladas en respuestas de error HTTP 500. Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor. Para obtener más información, consulte la [Página de excepciones para desarrolladores](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
