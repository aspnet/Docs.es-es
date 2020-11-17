---
title: Parte 2. Adición de un controlador a una aplicación de ASP.NET Core MVC
author: rick-anderson
description: Parte 2 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
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
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: 1851a61441ed351442f86939022319ad59d10006
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422638"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a>Parte 2. Adición de un controlador a una aplicación de ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

El patrón de arquitectura de Modelo-Vista-Controlador (MVC) separa una aplicación en tres componentes principales: **M** odelo, **v** ista y **c** ontrolador. El patrón de MVC ayuda a crear aplicaciones que son más fáciles de actualizar y probar que las tradicionales aplicaciones monolíticas. Las aplicaciones basadas en MVC contienen:

* **M** odelos: clases que representan los datos de la aplicación. Las clases de modelo usan lógica de validación para aplicar las reglas de negocio para esos datos. Normalmente, los objetos de modelo recuperan y almacenan el estado del modelo en una base de datos. En este tutorial, un modelo `Movie` recupera datos de películas de una base de datos, los proporciona a la vista o los actualiza. Los datos actualizados se escriben en una base de datos.

* **V** istas: Las vistas son los componentes que muestran la interfaz de usuario (IU) de la aplicación. Por lo general, esta interfaz de usuario muestra los datos del modelo.

* **C** ontroladores: clases que controlan las solicitudes del explorador. Recuperan los datos del modelo y llaman a plantillas de vistas que devuelven una respuesta. En una aplicación MVC, la vista solo muestra información; el controlador controla la interacción de los usuarios y los datos que introducen, y responde a ellos. Por ejemplo, el controlador controla los datos de enrutamiento y los valores de cadena de consulta y pasa estos valores al modelo. El modelo puede usar estos valores para consultar la base de datos. Por ejemplo, `https://localhost:5001/Home/Privacy` tiene datos de enrutamiento de `Home` (el controlador) y `Privacy` (el método de acción para llamar al controlador de inicio). `https://localhost:5001/Movies/Edit/5` es una solicitud para editar la película con ID=5 mediante el controlador de películas. Los datos de ruta se explican más adelante en el tutorial.

El patrón de MVC ayuda a crear aplicaciones que separan los diferentes aspectos de la aplicación (lógica de entrada, lógica comercial y lógica de la interfaz de usuario), a la vez que proporciona un acoplamiento vago entre estos elementos. El patrón especifica dónde debe ubicarse cada tipo de lógica en la aplicación. La lógica de la interfaz de usuario pertenece a la vista. La lógica de entrada pertenece al controlador. La lógica de negocios pertenece al modelo. Esta separación ayuda a administrar la complejidad al compilar una aplicación, ya que permite trabajar en uno de los aspectos de la implementación a la vez sin influir en el código de otro. Por ejemplo, puede trabajar en el código de vista sin depender del código de lógica de negocios.

En esta serie de tutoriales se tratarán estos conceptos y se mostrará cómo usarlos para crear una aplicación de película. El proyecto de MVC contiene carpetas para *controladores* y *vistas*.

## <a name="add-a-controller"></a>Incorporación de un controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Controlador**
  ![Explorador de soluciones, haga clic con el botón derecho en Controladores > Agregar Controlador](~/tutorials/first-mvc-app/adding-controller/_static/add_controllerCopy.png).

* En el cuadro de diálogo **Agregar Scaffold**, seleccione **MVC Controller - Empty** (Controlador MVC: en blanco)

  ![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app/adding-controller/_static/acCopy.png)

* En el cuadro de diálogo **Add Empty MVC Controller** (Agregar controlador MVC en blanco), escriba **HelloWorldController** y seleccione **AGREGAR**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seleccione el icono **EXPLORADOR**, presione Ctrl y haga clic con el botón derecho en **Controladores > Nuevo archivo** y asigne al nuevo archivo el nombre *HelloWorldController.cs*.

  ![Menú contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Nuevo archivo**.
![Menú contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Seleccione **ASP.NET Core** y **Clase de controlador**.

Asigne al controlador el nombre **HelloWorldController**.

![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Reemplace el contenido de *Controllers/HelloWorldController.cs* con lo siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Cada método `public` en un controlador puede ser invocado como un punto de conexión HTTP. En el ejemplo anterior, ambos métodos devuelven una cadena. Observe los comentarios delante de cada método.

Un extremo HTTP es una dirección URL que se puede usar como destino en la aplicación web, como por ejemplo `https://localhost:5001/HelloWorld`. Combina el protocolo usado `HTTPS`, la ubicación de red del servidor web (incluido el puerto TCP) `localhost:5001` y el URI de destino `HelloWorld`.

El primer comentario indica que se trata de un método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) que se invoca anexando `/HelloWorld/` a la dirección URL base. El segundo comentario especifica un método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) que se invoca anexando `/HelloWorld/Welcome/` a la dirección URL. Más adelante en el tutorial se usa el motor de scaffolding para generar métodos `HTTP POST` que actualizan los datos.

Ejecute la aplicación en modo de no depuración y anexione "HelloWorld" a la ruta de acceso en la barra de direcciones. El método `Index` devuelve una cadena.

![Ventana del explorador que muestra una respuesta de la aplicación a "This is my default action" (Esta es mi acción predeterminada)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC invoca las clases del controlador (y los métodos de acción que contienen) en función de la URL entrante. La [lógica de enrutamiento de URL](xref:mvc/controllers/routing) predeterminada que usa MVC emplea un formato similar al siguiente para determinar qué código se debe invocar:

`/[Controller]/[ActionName]/[Parameters]`

El formato de enrutamiento se establece en el método `Configure` del archivo *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Cuando se navega a la aplicación y no se suministra ningún segmento de dirección URL, de manera predeterminada se usan el controlador "Home" y el método "Index" especificados en la línea de plantilla resaltada arriba.

El primer segmento de dirección URL determina la clase de controlador que se va a ejecutar. Por tanto, `localhost:{PORT}/HelloWorld` se asigna a la clase **HelloWorld** Controller. La segunda parte del segmento de dirección URL determina el método de acción en la clase. De modo que `localhost:{PORT}/HelloWorld/Index` podría provocar que se ejecute el método `Index` de la clase `HelloWorldController`. Tenga en cuenta que solo es necesario navegar a `localhost:{PORT}/HelloWorld` para que se llame al método `Index` de manera predeterminada. Esto es porque `Index` es el método predeterminado al que se llamará en un controlador si no se especifica explícitamente un nombre de método. La tercera parte del segmento de dirección URL (`id`) es para los datos de ruta. Los datos de ruta se explican más adelante en el tutorial.

Vaya a `https://localhost:{PORT}/HelloWorld/Welcome`. El método `Welcome` se ejecuta y devuelve la cadena `This is the Welcome action method...`. Para esta dirección URL, el controlador es `HelloWorld` y `Welcome` es el método de acción. Todavía no ha usado el elemento `[Parameters]` de la dirección URL.

![Ventana del explorador que muestra la respuesta de la aplicación "This is the Welcome action method" (Este es el método de acción predeterminado)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modifique el código para pasar cierta información del parámetro desde la dirección URL al controlador. Por ejemplo: `/HelloWorld/Welcome?name=Rick&numtimes=4`. Cambie el método `Welcome` para que incluya dos parámetros, como se muestra en el código siguiente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

El código anterior:

* Usa la característica de parámetro opcional de C# para indicar que el parámetro `numTimes` tiene el valor predeterminado 1 si no se pasa ningún valor para ese parámetro. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` para proteger la aplicación de entradas malintencionadas (en concreto JavaScript).
* Usa [cadenas interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) en `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Ejecute la aplicación y navegue a:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Reemplace `{PORT}` por el número de puerto. Puede probar distintos valores para `name` y `numtimes` en la dirección URL. El sistema de [enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre de la cadena de consulta en la barra de direcciones a los parámetros del método. Vea [Model Binding](xref:mvc/models/model-binding) (Enlace de modelos) para más información.

![Ventana del explorador que muestra una respuesta de la aplicación de Hello Rick, NumTimes es\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

En la imagen anterior, el segmento de dirección URL (`Parameters`) no se usa y los parámetros `name` y `numTimes` se pasan en la [cadena de consulta](https://wikipedia.org/wiki/Query_string). El `?` (signo de interrogación) en la dirección URL anterior es un separador y le sigue la cadena de consulta. El carácter `&` separa los pares campo-valor.

Reemplace el método `Welcome` con el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Ejecute la aplicación y escriba la siguiente dirección URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Esta vez el tercer segmento de dirección URL coincide con el parámetro de ruta `id`. El método `Welcome` contiene un parámetro `id` que coincide con la plantilla de dirección URL en el método `MapControllerRoute`. El elemento `?` final (en `id?`) indica que el parámetro `id` es opcional.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

En estos ejemplos, el controlador ha realizado la parte "VC" de MVC, es decir, el trabajo de **V** ista y de **C** ontrolador. El controlador devuelve HTML directamente. Por lo general, no es aconsejable que los controles devuelvan HTML directamente, porque resulta muy complicado de programar y mantener. En vez de ello, se suele usar un archivo de plantilla de vista de Razor independiente para generar la respuesta HTML. Haremos esto en el siguiente tutorial.

> [!div class="step-by-step"]
> [Anterior](start-mvc.md)
> [Siguiente](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

El patrón de arquitectura de Modelo-Vista-Controlador (MVC) separa una aplicación en tres componentes principales: **M** odelo, **v** ista y **c** ontrolador. El patrón de MVC ayuda a crear aplicaciones que son más fáciles de actualizar y probar que las tradicionales aplicaciones monolíticas. Las aplicaciones basadas en MVC contienen:

* **M** odelos: clases que representan los datos de la aplicación. Las clases de modelo usan lógica de validación para aplicar las reglas de negocio para esos datos. Normalmente, los objetos de modelo recuperan y almacenan el estado del modelo en una base de datos. En este tutorial, un modelo `Movie` recupera datos de películas de una base de datos, los proporciona a la vista o los actualiza. Los datos actualizados se escriben en una base de datos.

* **V** istas: Las vistas son los componentes que muestran la interfaz de usuario (IU) de la aplicación. Por lo general, esta interfaz de usuario muestra los datos del modelo.

* **C** ontroladores: clases que controlan las solicitudes del explorador. Recuperan los datos del modelo y llaman a plantillas de vistas que devuelven una respuesta. En una aplicación MVC, la vista solo muestra información; el controlador controla la interacción de los usuarios y los datos que introducen, y responde a ellos. Por ejemplo, el controlador controla los datos de enrutamiento y los valores de cadena de consulta y pasa estos valores al modelo. El modelo puede usar estos valores para consultar la base de datos. Por ejemplo, `https://localhost:5001/Home/About` tiene datos de enrutamiento de `Home` (el controlador) y `About` (el método de acción para llamar al controlador de inicio). `https://localhost:5001/Movies/Edit/5` es una solicitud para editar la película con ID=5 mediante el controlador de películas. Los datos de ruta se explican más adelante en el tutorial.

El patrón de MVC ayuda a crear aplicaciones que separan los diferentes aspectos de la aplicación (lógica de entrada, lógica comercial y lógica de la interfaz de usuario), a la vez que proporciona un acoplamiento vago entre estos elementos. El patrón especifica dónde debe ubicarse cada tipo de lógica en la aplicación. La lógica de la interfaz de usuario pertenece a la vista. La lógica de entrada pertenece al controlador. La lógica de negocios pertenece al modelo. Esta separación ayuda a administrar la complejidad al compilar una aplicación, ya que permite trabajar en uno de los aspectos de la implementación a la vez sin influir en el código de otro. Por ejemplo, puede trabajar en el código de vista sin depender del código de lógica de negocios.

En esta serie de tutoriales se tratarán estos conceptos y se mostrará cómo usarlos para crear una aplicación de película. El proyecto de MVC contiene carpetas para *controladores* y *vistas*.

## <a name="add-a-controller"></a>Incorporación de un controlador

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Controlador**
  ![Menú contextual](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png).

* En el cuadro de diálogo **Agregar Scaffold**, seleccione **MVC Controller - Empty** (Controlador MVC: en blanco)

  ![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* En el cuadro de diálogo **Add Empty MVC Controller** (Agregar controlador MVC en blanco), escriba **HelloWorldController** y seleccione **AGREGAR**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Seleccione el icono **EXPLORADOR**, presione Ctrl y haga clic con el botón derecho en **Controladores > Nuevo archivo** y asigne al nuevo archivo el nombre *HelloWorldController.cs*.

  ![Menú contextual](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

En el **Explorador de soluciones**, haga clic con el botón derecho en **Controladores > Agregar > Nuevo archivo**.
![Menú contextual](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Seleccione **ASP.NET Core** y **Clase de controlador de MVC**.

Asigne al controlador el nombre **HelloWorldController**.

![Agregar un controlador de MVC y asignarle un nombre](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Reemplace el contenido de *Controllers/HelloWorldController.cs* con lo siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Cada método `public` en un controlador puede ser invocado como un punto de conexión HTTP. En el ejemplo anterior, ambos métodos devuelven una cadena. Observe los comentarios delante de cada método.

Un extremo HTTP es una dirección URL que se puede usar como destino en la aplicación web, como por ejemplo `https://localhost:5001/HelloWorld`. Combina el protocolo usado `HTTPS`, la ubicación de red del servidor web (incluido el puerto TCP) `localhost:5001` y el URI de destino `HelloWorld`.

El primer comentario indica que se trata de un método [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) que se invoca anexando `/HelloWorld/` a la dirección URL base. El segundo comentario especifica un método [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) que se invoca anexando `/HelloWorld/Welcome/` a la dirección URL. Más adelante en el tutorial se usa el motor de scaffolding para generar métodos `HTTP POST` que actualizan los datos.

Ejecute la aplicación en modo de no depuración y anexione "HelloWorld" a la ruta de acceso en la barra de direcciones. El método `Index` devuelve una cadena.

![Ventana del explorador que muestra una respuesta de la aplicación a "This is my default action" (Esta es mi acción predeterminada)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC invoca las clases del controlador (y los métodos de acción que contienen) en función de la URL entrante. La [lógica de enrutamiento de URL](xref:mvc/controllers/routing) predeterminada que usa MVC emplea un formato similar al siguiente para determinar qué código se debe invocar:

`/[Controller]/[ActionName]/[Parameters]`

El formato de enrutamiento se establece en el método `Configure` del archivo *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Cuando se navega a la aplicación y no se suministra ningún segmento de dirección URL, de manera predeterminada se usan el controlador "Home" y el método "Index" especificados en la línea de plantilla resaltada arriba.

El primer segmento de dirección URL determina la clase de controlador que se va a ejecutar. De modo que `localhost:{PORT}/HelloWorld` se asigna a la clase `HelloWorldController`. La segunda parte del segmento de dirección URL determina el método de acción en la clase. De modo que `localhost:{PORT}/HelloWorld/Index` podría provocar que se ejecute el método `Index` de la clase `HelloWorldController`. Tenga en cuenta que solo es necesario navegar a `localhost:{PORT}/HelloWorld` para que se llame al método `Index` de manera predeterminada. Esto es porque `Index` es el método predeterminado al que se llamará en un controlador si no se especifica explícitamente un nombre de método. La tercera parte del segmento de dirección URL (`id`) es para los datos de ruta. Los datos de ruta se explican más adelante en el tutorial.

Vaya a `https://localhost:{PORT}/HelloWorld/Welcome`. El método `Welcome` se ejecuta y devuelve la cadena `This is the Welcome action method...`. Para esta dirección URL, el controlador es `HelloWorld` y `Welcome` es el método de acción. Todavía no ha usado el elemento `[Parameters]` de la dirección URL.

![Ventana del explorador que muestra la respuesta de la aplicación "This is the Welcome action method" (Este es el método de acción predeterminado)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Modifique el código para pasar cierta información del parámetro desde la dirección URL al controlador. Por ejemplo: `/HelloWorld/Welcome?name=Rick&numtimes=4`. Cambie el método `Welcome` para que incluya dos parámetros, como se muestra en el código siguiente.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

El código anterior:

* Usa la característica de parámetro opcional de C# para indicar que el parámetro `numTimes` tiene el valor predeterminado 1 si no se pasa ningún valor para ese parámetro. <!-- remove for simplified -->
* Usa `HtmlEncoder.Default.Encode` para proteger la aplicación de entradas malintencionadas (en concreto JavaScript).
* Usa [cadenas interpoladas](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) en `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Ejecute la aplicación y navegue a:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Reemplace `{PORT}` por el número de puerto. Puede probar distintos valores para `name` y `numtimes` en la dirección URL. El sistema de [enlace de modelos](xref:mvc/models/model-binding) de MVC asigna automáticamente los parámetros con nombre de la cadena de consulta en la barra de direcciones a los parámetros del método. Vea [Model Binding](xref:mvc/models/model-binding) (Enlace de modelos) para más información.

![Ventana del explorador que muestra una respuesta de la aplicación de Hello Rick, NumTimes es\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

En la imagen anterior, el segmento de dirección URL (`Parameters`) no se usa y los parámetros `name` y `numTimes` se pasan en la [cadena de consulta](https://wikipedia.org/wiki/Query_string). El `?` (signo de interrogación) en la dirección URL anterior es un separador y le sigue la cadena de consulta. El carácter `&` separa los pares campo-valor.

Reemplace el método `Welcome` con el código siguiente:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Ejecute la aplicación y escriba la siguiente dirección URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Esta vez el tercer segmento de dirección URL coincide con el parámetro de ruta `id`. El método `Welcome` contiene un parámetro `id` que coincide con la plantilla de dirección URL en el método `MapRoute`. El elemento `?` final (en `id?`) indica que el parámetro `id` es opcional.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

En estos ejemplos, el controlador ha realizado la parte "VC" de MVC, es decir, el trabajo de vista y de controlador. El controlador devuelve HTML directamente. Por lo general, no es aconsejable que los controles devuelvan HTML directamente, porque resulta muy complicado de programar y mantener. En su lugar, se suele usar un archivo de plantilla de vista de Razor independiente para ayudar a generar la respuesta HTML. Haremos esto en el siguiente tutorial.

> [!div class="step-by-step"]
> [Anterior](start-mvc.md)
> [Siguiente](adding-view.md)

::: moniker-end
