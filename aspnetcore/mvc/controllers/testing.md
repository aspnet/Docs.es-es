---
title: Probar la lógica del controlador en ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo probar la lógica del controlador en ASP.NET Core con Moq y xUnit.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 311f1ce9eb2b0e358ddc01a1982dc39379f53b8f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020917"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Lógica del controlador de pruebas unitarias en ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) implican probar una parte de una aplicación de forma aislada con respecto a su infraestructura y dependencias. Cuando se realizan pruebas unitarias de la lógica de controlador, solo se comprueba el contenido de una única acción, no el comportamiento de sus dependencias o del marco en sí.

## <a name="unit-testing-controllers"></a>Controladores de pruebas unitarias

Configure pruebas unitarias de las acciones del controlador para centrarse en el comportamiento del controlador. Una prueba unitaria del controlador evita escenarios como [filtros](xref:mvc/controllers/filters), [enrutamiento](xref:fundamentals/routing) y [enlace de modelos](xref:mvc/models/model-binding). Las pruebas que abarcan las interacciones entre los componentes que colectivamente responden a una solicitud se controlan mediante *pruebas de integración*. Para más información sobre las pruebas de integración, vea <xref:test/integration-tests>.

Si va a escribir filtros personalizados y rutas, realice pruebas unitarias en ellos de forma aislada, no como parte de las pruebas de una acción de controlador concreta.

Para demostrar las pruebas unitarias del controlador, revise el siguiente controlador en la aplicación de ejemplo. 

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

El controlador Home muestra una lista de sesiones de lluvia de ideas y permite crear nuevas sesiones de lluvia de ideas con una solicitud POST:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

El controlador anterior:

* Sigue el [Principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Espera la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) para ofrecer una instancia de `IBrainstormSessionRepository`.
* Se puede probar con un servicio `IBrainstormSessionRepository` ficticio con el uso de un marco de objeto ficticio, como [Moq](https://www.nuget.org/packages/Moq/). Un *objeto ficticio* es un objeto fabricado con un conjunto predeterminado de comportamientos de propiedades y métodos utilizados para las pruebas. Para más información, vea [Introducción a las pruebas de integración](xref:test/integration-tests#introduction-to-integration-tests).

El método `HTTP GET Index` no tiene bucles ni bifurcaciones y solamente llama a un método. La prueba unitaria para esta acción:

* Realice el simulacro del servicio `IBrainstormSessionRepository` mediante el método `GetTestSessions`. `GetTestSessions` crea dos sesiones de lluvia de ideas ficticias con fechas y nombres de sesión.
* Ejecuta el método `Index`.
* Realiza aserciones sobre el resultado devuelto por el método:
  * Se devuelve <xref:Microsoft.AspNetCore.Mvc.ViewResult>.
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) es `StormSessionViewModel`.
  * Hay dos sesiones de lluvia de ideas almacenadas en `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Las pruebas del método `HTTP POST Index` del controlador Home verifican que:

* Cuando [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) es `false` , el método de acción devuelve una *Solicitud incorrecta 400* <xref:Microsoft.AspNetCore.Mvc.ViewResult> con los datos adecuados.
* Cuando `ModelState.IsValid` es `true`:
  * Se llama al método `Add` del repositorio.
  * Se devuelve <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> con los argumentos correctos.

Un estado de modelo no válido se comprueba mediante la adición de errores con <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, como se muestra en la primera prueba de abajo:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Si [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) no es válido, se devuelve el mismo elemento `ViewResult` que para una solicitud GET. La prueba no intenta pasar un modelo no válido. Pasar un modelo no válido no es un enfoque válido, ya que el enlace de modelos no está en ejecución (aunque una [prueba de integración](xref:test/integration-tests) usa el enlace de modelos). En este caso, no se comprueba el enlace de modelos. Estas pruebas unitarias solo comprueban el código del método de acción.

La segunda prueba verifica que, cuando `ModelState` es válido:

* Se agrega un nuevo elemento `BrainstormSession` (mediante el repositorio).
* El método devuelve un elemento `RedirectToActionResult` con las propiedades esperadas.

Las llamadas ficticias que no se efectúan se suelen ignorar, aunque llamar a `Verifiable` al final de la llamada de configuración permite realizar una validación ficticia de la prueba. Esto se realiza con una llamada a `mockRepo.Verify`, que producirá un error en la prueba si no se ha llamado al método esperado.

> [!NOTE]
> La biblioteca Moq usada en este ejemplo permite mezclar fácilmente objetos ficticios comprobables o "estrictos" con objetos ficticios no comprobables (también denominados "flexibles" o stub). Obtenga más información sobre cómo [personalizar el comportamiento de objetos ficticios con Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) en la aplicación de ejemplo muestra información relacionada con una sesión de lluvia de ideas determinada. El controlador incluye lógica para tratar valores `id` no válidos (hay dos escenarios `return` en el ejemplo siguiente para abarcar estos escenarios). La última instrucción `return` devuelve un `StormSessionViewModel` nuevo a la vista (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Las pruebas unitarias incluyen una prueba de cada escenario `return` en la acción `Index` del controlador de sesión:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Al pasar al controlador de ideas, la aplicación expone la funcionalidad como una API web en la ruta `api/ideas`:

* El método `ForSession` devuelve una lista de ideas (`IdeaDTO`) asociadas con una sesión de lluvia de ideas.
* El método `Create` agrega nuevas ideas a una sesión.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Evite devolver entidades de dominio de negocio directamente mediante llamadas API. Entidades de dominio:

* Suelen incluir más datos de los que necesita el cliente.
* Innecesariamente acoplan el modelo de dominio interno de la aplicación con la API expuesta públicamente.

La asignación entre las entidades de dominio y los tipos devueltos al cliente se puede realizar:

* Manualmente, con una operación `Select` de LINQ, como la aplicación de ejemplo usa. Para más información, vea [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automáticamente, con una biblioteca, como [AutoMapper](https://github.com/AutoMapper/AutoMapper).

A continuación, la aplicación de ejemplo muestra las pruebas unitarias para los métodos de API `Create` y `ForSession` del controlador de ideas.

La aplicación de ejemplo contiene dos pruebas `ForSession`. La primera prueba determina si `ForSession` devuelve <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP no encontrado) para una sesión no válida:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

La segunda prueba `ForSession` determina si `ForSession` devuelve una lista de ideas de sesión (`<List<IdeaDTO>>`) para una sesión válida. Las comprobaciones también examinan la primera idea para confirmar si su propiedad `Name` es correcta:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Para comprobar el comportamiento del método `Create` cuando `ModelState` no es válido, la aplicación de ejemplo agrega un error de modelo al controlador como parte de la prueba. No intente probar la validación del modelo o el enlace de modelos en las pruebas unitarias; céntrese tan solo en el comportamiento del método de acción al confrontarlo con un valor de `ModelState` no válido:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

La segunda prueba de `Create` depende de que el repositorio devuelva `null`, por lo que el repositorio ficticio está configurado para devolver `null`. No es necesario crear una base de datos de prueba (en memoria o de cualquier otro modo) ni crear una consulta que devuelva este resultado. La prueba puede realizarse en una sola instrucción, como se muestra en el código de ejemplo:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

La tercera prueba de `Create`, `Create_ReturnsNewlyCreatedIdeaForSession`, verifica que se llama al método `UpdateAsync` del repositorio. Se llama al objeto ficticio con `Verifiable` y, después, se llama al método `Verify` del repositorio ficticio para confirmar que se ejecutó el método Verifiable. La prueba unitaria no es responsable de garantizar que el método `UpdateAsync` guarda los datos; esto se puede realizar con una prueba de integración.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Probar ActionResult\<T>

En ASP.net Core 2,1 o posterior, [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) permite devolver un tipo que deriva de `ActionResult` o devuelve un tipo específico.

La aplicación de ejemplo incluye un método que devuelve un resultado `List<IdeaDTO>` para una sesión determinada `id`. Si la sesión `id` no existe, el controlador devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dos pruebas del controlador `ForSessionActionResult` se incluyen en `ApiIdeasControllerTests`.

La primera prueba confirma que el controlador devuelve `ActionResult`, pero no una lista inexistente de ideas para una sesión inexistente `id`:

* El tipo `ActionResult` es `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> es <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Para obtener un elemento `id` de sesión válido, la segunda prueba confirma que el método devuelve:

* Un `ActionResult` con un tipo `List<IdeaDTO>`.
* [ActionResult \<T> . El valor](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) es un `List<IdeaDTO>` tipo.
* El primer elemento de la lista es una idea válida que coincide con la idea almacenada en la sesión ficticia (obtenido mediante una llamada a `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

La aplicación de ejemplo también incluye un método para crear un nuevo elemento `Idea` para una sesión determinada. El controlador devuelve:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> para un modelo no válido.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si no existe la sesión.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> cuando se actualiza la sesión con la idea nueva.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Tres pruebas de `CreateActionResult` se incluyen en `ApiIdeasControllerTests`.

El primer texto confirma que se devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> para un modelo no válido.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

La segunda prueba comprueba que se devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si la sesión no existe.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Para una sesión válida `id`, la prueba final confirma que:

* El método devuelve `ActionResult` con un tipo `BrainstormSession`.
* [ActionResult \<T> . El resultado](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) es <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` es similar a una respuesta *201 Creado* con un encabezado `Location`.
* [ActionResult \<T> . El valor](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) es un `BrainstormSession` tipo.
* La llamada ficticia para actualizar la sesión, `UpdateAsync(testSession)`, se ha invocado. La llamada al método `Verifiable` se comprueba mediante la ejecución de `mockRepo.Verify()` en las aserciones.
* Se devuelven dos objetos `Idea` para la sesión.
* El último elemento (el elemento `Idea` agregado por la llamada ficticia a `UpdateAsync`) coincide con el elemento `newIdea` agregado a la sesión en la prueba.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Los [controles](xref:mvc/controllers/actions) desempeñan un rol fundamental en cualquier aplicación de ASP.NET Core MVC. Por tanto, debe tener la seguridad de que los controladores se comportan según lo previsto. Las pruebas automatizadas pueden detectar errores antes de que la aplicación se implemente en un entorno de producción.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Pruebas unitarias de la lógica del controlador

Las [pruebas unitarias](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) implican probar una parte de una aplicación de forma aislada con respecto a su infraestructura y dependencias. Cuando se realizan pruebas unitarias de la lógica de controlador, solo se comprueba el contenido de una única acción, no el comportamiento de sus dependencias o del marco en sí.

Configure pruebas unitarias de las acciones del controlador para centrarse en el comportamiento del controlador. Una prueba unitaria del controlador evita escenarios como [filtros](xref:mvc/controllers/filters), [enrutamiento](xref:fundamentals/routing) y [enlace de modelos](xref:mvc/models/model-binding). Las pruebas que abarcan las interacciones entre los componentes que colectivamente responden a una solicitud se controlan mediante *pruebas de integración*. Para más información sobre las pruebas de integración, vea <xref:test/integration-tests>.

Si va a escribir filtros personalizados y rutas, realice pruebas unitarias en ellos de forma aislada, no como parte de las pruebas de una acción de controlador concreta.

Para demostrar las pruebas unitarias del controlador, revise el siguiente controlador en la aplicación de ejemplo. El controlador Home muestra una lista de sesiones de lluvia de ideas y permite crear nuevas sesiones de lluvia de ideas con una solicitud POST:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

El controlador anterior:

* Sigue el [Principio de dependencias explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Espera la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) para ofrecer una instancia de `IBrainstormSessionRepository`.
* Se puede probar con un servicio `IBrainstormSessionRepository` ficticio con el uso de un marco de objeto ficticio, como [Moq](https://www.nuget.org/packages/Moq/). Un *objeto ficticio* es un objeto fabricado con un conjunto predeterminado de comportamientos de propiedades y métodos utilizados para las pruebas. Para más información, vea [Introducción a las pruebas de integración](xref:test/integration-tests#introduction-to-integration-tests).

El método `HTTP GET Index` no tiene bucles ni bifurcaciones y solamente llama a un método. La prueba unitaria para esta acción:

* Realice el simulacro del servicio `IBrainstormSessionRepository` mediante el método `GetTestSessions`. `GetTestSessions` crea dos sesiones de lluvia de ideas ficticias con fechas y nombres de sesión.
* Ejecuta el método `Index`.
* Realiza aserciones sobre el resultado devuelto por el método:
  * Se devuelve <xref:Microsoft.AspNetCore.Mvc.ViewResult>.
  * [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) es `StormSessionViewModel`.
  * Hay dos sesiones de lluvia de ideas almacenadas en `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Las pruebas del método `HTTP POST Index` del controlador Home verifican que:

* Cuando [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) es `false` , el método de acción devuelve una *Solicitud incorrecta 400* <xref:Microsoft.AspNetCore.Mvc.ViewResult> con los datos adecuados.
* Cuando `ModelState.IsValid` es `true`:
  * Se llama al método `Add` del repositorio.
  * Se devuelve <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> con los argumentos correctos.

Un estado de modelo no válido se comprueba mediante la adición de errores con <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, como se muestra en la primera prueba de abajo:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Si [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) no es válido, se devuelve el mismo elemento `ViewResult` que para una solicitud GET. La prueba no intenta pasar un modelo no válido. Pasar un modelo no válido no es un enfoque válido, ya que el enlace de modelos no está en ejecución (aunque una [prueba de integración](xref:test/integration-tests) usa el enlace de modelos). En este caso, no se comprueba el enlace de modelos. Estas pruebas unitarias solo comprueban el código del método de acción.

La segunda prueba verifica que, cuando `ModelState` es válido:

* Se agrega un nuevo elemento `BrainstormSession` (mediante el repositorio).
* El método devuelve un elemento `RedirectToActionResult` con las propiedades esperadas.

Las llamadas ficticias que no se efectúan se suelen ignorar, aunque llamar a `Verifiable` al final de la llamada de configuración permite realizar una validación ficticia de la prueba. Esto se realiza con una llamada a `mockRepo.Verify`, que producirá un error en la prueba si no se ha llamado al método esperado.

> [!NOTE]
> La biblioteca Moq usada en este ejemplo permite mezclar fácilmente objetos ficticios comprobables o "estrictos" con objetos ficticios no comprobables (también denominados "flexibles" o stub). Obtenga más información sobre cómo [personalizar el comportamiento de objetos ficticios con Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) en la aplicación de ejemplo muestra información relacionada con una sesión de lluvia de ideas determinada. El controlador incluye lógica para tratar valores `id` no válidos (hay dos escenarios `return` en el ejemplo siguiente para abarcar estos escenarios). La última instrucción `return` devuelve un `StormSessionViewModel` nuevo a la vista (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Las pruebas unitarias incluyen una prueba de cada escenario `return` en la acción `Index` del controlador de sesión:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Al pasar al controlador de ideas, la aplicación expone la funcionalidad como una API web en la ruta `api/ideas`:

* El método `ForSession` devuelve una lista de ideas (`IdeaDTO`) asociadas con una sesión de lluvia de ideas.
* El método `Create` agrega nuevas ideas a una sesión.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Evite devolver entidades de dominio de negocio directamente mediante llamadas API. Entidades de dominio:

* Suelen incluir más datos de los que necesita el cliente.
* Innecesariamente acoplan el modelo de dominio interno de la aplicación con la API expuesta públicamente.

La asignación entre las entidades de dominio y los tipos devueltos al cliente se puede realizar:

* Manualmente, con una operación `Select` de LINQ, como la aplicación de ejemplo usa. Para más información, vea [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automáticamente, con una biblioteca, como [AutoMapper](https://github.com/AutoMapper/AutoMapper).

A continuación, la aplicación de ejemplo muestra las pruebas unitarias para los métodos de API `Create` y `ForSession` del controlador de ideas.

La aplicación de ejemplo contiene dos pruebas `ForSession`. La primera prueba determina si `ForSession` devuelve <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP no encontrado) para una sesión no válida:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

La segunda prueba `ForSession` determina si `ForSession` devuelve una lista de ideas de sesión (`<List<IdeaDTO>>`) para una sesión válida. Las comprobaciones también examinan la primera idea para confirmar si su propiedad `Name` es correcta:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Para comprobar el comportamiento del método `Create` cuando `ModelState` no es válido, la aplicación de ejemplo agrega un error de modelo al controlador como parte de la prueba. No intente probar la validación del modelo o el enlace de modelos en las pruebas unitarias; céntrese tan solo en el comportamiento del método de acción al confrontarlo con un valor de `ModelState` no válido:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

La segunda prueba de `Create` depende de que el repositorio devuelva `null`, por lo que el repositorio ficticio está configurado para devolver `null`. No es necesario crear una base de datos de prueba (en memoria o de cualquier otro modo) ni crear una consulta que devuelva este resultado. La prueba puede realizarse en una sola instrucción, como se muestra en el código de ejemplo:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

La tercera prueba de `Create`, `Create_ReturnsNewlyCreatedIdeaForSession`, verifica que se llama al método `UpdateAsync` del repositorio. Se llama al objeto ficticio con `Verifiable` y, después, se llama al método `Verify` del repositorio ficticio para confirmar que se ejecutó el método Verifiable. La prueba unitaria no es responsable de garantizar que el método `UpdateAsync` guarda los datos; esto se puede realizar con una prueba de integración.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Probar ActionResult\<T>

En ASP.net Core 2,1 o posterior, [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) permite devolver un tipo que deriva de `ActionResult` o devuelve un tipo específico.

La aplicación de ejemplo incluye un método que devuelve un resultado `List<IdeaDTO>` para una sesión determinada `id`. Si la sesión `id` no existe, el controlador devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dos pruebas del controlador `ForSessionActionResult` se incluyen en `ApiIdeasControllerTests`.

La primera prueba confirma que el controlador devuelve `ActionResult`, pero no una lista inexistente de ideas para una sesión inexistente `id`:

* El tipo `ActionResult` es `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> es <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Para obtener un elemento `id` de sesión válido, la segunda prueba confirma que el método devuelve:

* Un `ActionResult` con un tipo `List<IdeaDTO>`.
* [ActionResult \<T> . El valor](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) es un `List<IdeaDTO>` tipo.
* El primer elemento de la lista es una idea válida que coincide con la idea almacenada en la sesión ficticia (obtenido mediante una llamada a `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

La aplicación de ejemplo también incluye un método para crear un nuevo elemento `Idea` para una sesión determinada. El controlador devuelve:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> para un modelo no válido.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si no existe la sesión.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> cuando se actualiza la sesión con la idea nueva.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Tres pruebas de `CreateActionResult` se incluyen en `ApiIdeasControllerTests`.

El primer texto confirma que se devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> para un modelo no válido.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

La segunda prueba comprueba que se devuelve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> si la sesión no existe.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Para una sesión válida `id`, la prueba final confirma que:

* El método devuelve `ActionResult` con un tipo `BrainstormSession`.
* [ActionResult \<T> . El resultado](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) es <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` es similar a una respuesta *201 Creado* con un encabezado `Location`.
* [ActionResult \<T> . El valor](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) es un `BrainstormSession` tipo.
* La llamada ficticia para actualizar la sesión, `UpdateAsync(testSession)`, se ha invocado. La llamada al método `Verifiable` se comprueba mediante la ejecución de `mockRepo.Verify()` en las aserciones.
* Se devuelven dos objetos `Idea` para la sesión.
* El último elemento (el elemento `Idea` agregado por la llamada ficticia a `UpdateAsync`) coincide con el elemento `newIdea` agregado a la sesión en la prueba.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:test/integration-tests>
* [Crear y ejecutar pruebas unitarias con Visual Studio](/visualstudio/test/unit-test-your-code)
* [MyTest. AspNetCore. Mvc: biblioteca de pruebas fluidas para ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): biblioteca de pruebas unitarias fuertemente tipadas, que proporciona una interfaz fluida para probar MVC y aplicaciones de API Web. (*Microsoft no realiza su mantenimiento ni su soporte técnico.* )
* [JustMockLite](https://github.com/telerik/JustMockLite): marco ficticio para desarrolladores de .NET. (*Microsoft no realiza su mantenimiento ni su soporte técnico.* )

