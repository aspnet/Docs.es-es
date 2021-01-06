---
title: Pruebas unitarias de Razor Pages en ASP.NET Core
author: rick-anderson
description: Aprenda a crear pruebas unitarias de aplicaciones de Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058043"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>Pruebas unitarias de Razor Pages en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core admite pruebas unitarias de aplicaciones de Razor Pages. Las pruebas de la capa de acceso a datos (DAL) y de los modelos de página ayudan a garantizar lo siguiente:

* Las partes de una aplicación de Razor Pages funcionan de forma independiente y conjunta como una unidad durante la creación de una aplicación.
* Las clases y los métodos tienen ámbitos de responsabilidad restringidos.
* Hay documentación adicional disponible sobre cómo debe comportarse la aplicación.
* Se detectan regresiones —que son errores que salen a la luz a raíz de las actualizaciones de código— en los procesos de compilación e implementación automatizados.

En este tema se da por hecho que el usuario posee conocimientos básicos de las pruebas unitarias y las aplicaciones de Razor Pages. Si no está familiarizado con los conceptos de prueba o aplicaciones de Razor Pages, vea los siguientes temas:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Prueba unitaria de C# en .NET Core mediante pruebas de dotnet y xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

El proyecto de ejemplo se compone de dos aplicaciones:

| Aplicación         | Carpeta del proyecto                     | Descripción |
| ----------- | ---------------------------------- | ----------- |
| Aplicación de mensajes | *src/RazorPagesTestSample*         | Permite a un usuario agregar un mensaje, eliminar un mensaje, eliminar todos los mensajes y analizar mensajes (hallar la media de palabras por mensaje). |
| Probar la aplicación    | *tests/RazorPagesTestSample.Tests* | Sirve para realizar una prueba unitaria del modelo de página Index y la DAL de la aplicación de mensajes. |

Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](/visualstudio/test/unit-test-your-code) o [Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema en la carpeta *tests/RazorPagesTestSample.Tests*:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organización de la aplicación de mensajes

La aplicación de mensajes es un sistema de mensajes de Razor Pages con las siguientes características:

* La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, eliminación y análisis de mensajes (hallar la media de palabras por mensaje).
* La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje). Se necesita la propiedad `Text`, que está limitada a 200 caracteres.
* Los mensajes se almacenan en la [base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* La aplicación contiene una DAL en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*). Los métodos de DAL se marcan como `virtual`, lo que permite realizar simulaciones en ellos para usarlos en las pruebas.
* Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes. Estos *mensajes inicializados* también se usan en las pruebas.

&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria con las pruebas con MSTest. En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/). Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.

Aunque la aplicación de ejemplo no usa el patrón del repositorio y no es un ejemplo eficaz del [patrón Unit of Work (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo. Para más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y <xref:mvc/controllers/testing> (el ejemplo implementa el patrón del repositorio).

## <a name="test-app-organization"></a>Organización de la aplicación de prueba

La aplicación de prueba es una aplicación de consola dentro de la carpeta *tests/RazorPagesTestSample.Tests*.

| Carpeta de aplicación de prueba | Descripción |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contiene las pruebas unitarias de la DAL.</li><li>*IndexPageTests.cs* contiene las pruebas unitarias del modelo de página Index.</li></ul> |
| *Utilities*     | Contiene el método `TestDbContextOptions` empleado para crear nuevas opciones de contexto de base de datos para cada prueba unitaria de DAL, de modo que la base de datos se restablezca a su condición de línea base en cada prueba. |

El marco de pruebas es [xUnit](https://xunit.github.io/). El marco de trabajo de simulación de objetos es [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Pruebas unitarias de la capa de acceso a datos (DAL)

La aplicación de mensajes tiene una DAL con cuatro métodos contenidos en la clase `AppDbContext` (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Cada método tiene una o dos pruebas unitarias en la aplicación de prueba.

| Método de DAL               | Función                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtiene un elemento `List<Message>` de la base de datos ordenada por la propiedad `Text`. |
| `AddMessageAsync`        | Agrega un elemento `Message` a la base de datos.                                          |
| `DeleteAllMessagesAsync` | Elimina todas las entradas `Message` de la base de datos.                           |
| `DeleteMessageAsync`     | Elimina todas las entradas `Message` de la base de datos según `Id`.                      |

Las pruebas unitarias de la DAL requieren <xref:Microsoft.EntityFrameworkCore.DbContextOptions> al crear un elemento `AppDbContext` para cada prueba. Un método para crear el elemento `DbContextOptions` de cada prueba es usar un elemento <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

El inconveniente de este método es que cada prueba recibe la base de datos en el estado en el que la dejó la prueba anterior. Esto puede ser problemático al intentar escribir pruebas unitarias atómicas que no interfieran entre sí. Para forzar que `AppDbContext` use un nuevo contexto de base de datos en cada prueba, proporcione una instancia de `DbContextOptions` que esté basada en un nuevo proveedor de servicios. La aplicación de prueba muestra cómo llevar esto a cabo usando el método `TestDbContextOptions` de su clase `Utilities` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

El uso de `DbContextOptions` en las pruebas unitarias de DAL permite que cada prueba se ejecute de forma atómica con una instancia de base de datos completamente nueva:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de prueba de la clase `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest.cs*) sigue un patrón Organización-Acción-Aserción similar:

1. Organización: la base de datos se configura para la prueba y/o el resultado previsto se define.
1. Acción: la prueba se ejecuta.
1. Aserción: se realizan aserciones para determinar si el resultado de la prueba es correcto.

Por ejemplo, el método `DeleteMessageAsync` es responsable de quitar un mensaje individual identificado por su `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Hay dos pruebas para este método: una comprueba que el método elimina un mensaje cuando el mensaje está presente en la base de datos y la otra, que la base de datos no cambia si el `Id` del mensaje que se quiere eliminar no existe. Aquí mostramos el método `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

En primer lugar, el método lleva a cabo el paso de organización, donde se prepara todo para el paso de acción. Los mensajes de inicialización se obtienen y se conservan en `seedMessages`. Los mensajes de inicialización se guardan en la base de datos. El mensaje con un `Id` de `1` se establece para eliminarse. Cuando el método `DeleteMessageAsync` se ejecuta, los mensajes esperados deben incluir todos los mensajes, excepto el que tenga un `Id` de `1`. La variable `expectedMessages` representa el resultado esperado.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

El método actúa: El método `DeleteMessageAsync` se ejecuta pasando un `recId` de `1`:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Por último, el método obtiene el elemento `Messages` del contexto y lo compara con el elemento `expectedMessages`, afirmando que los dos son iguales:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que los dos elementos `List<Message>` son iguales:

* Los mensajes se ordenan por `Id`.
* Los pares de mensajes se comparan en la propiedad `Text`.

Un método de prueba similar, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`, comprueba el resultado de intentar eliminar un mensaje que no existe. En este caso, los mensajes esperados en la base de datos deben ser iguales a los mensajes reales después de que el método `DeleteMessageAsync` se ejecute. No debe haber ningún cambio en el contenido de la base de datos:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Pruebas unitarias de los métodos del modelo de página

Otro conjunto de pruebas unitarias es responsable de las pruebas de los métodos del modelo de página. En la aplicación de mensajes, los modelos de página Index se encuentran en la clase `IndexModel` en *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Método del modelo de página | Función |
| ----------------- | -------- |
| `OnGetAsync` | Obtiene los mensajes de la DAL de la interfaz de usuario mediante el método `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Si [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) es válido, se llama a `AddMessageAsync` para agregar un mensaje a la base de datos. |
| `OnPostDeleteAllMessagesAsync` | Llama a `DeleteAllMessagesAsync` para eliminar todos los mensajes de la base de datos. |
| `OnPostDeleteMessageAsync` | Ejecuta `DeleteMessageAsync` para eliminar un mensaje con el `Id` especificado. |
| `OnPostAnalyzeMessagesAsync` | Si hay uno o más mensajes en la base de datos, calcula la media de palabras por mensaje. |

Los métodos del modelo de página se comprueban con siete pruebas en la clase `IndexPageTests` (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Las pruebas usan el conocido patrón Organización-Aserción-Acción. Estas pruebas se centran en lo siguiente:

* Determinar si los métodos siguen el comportamiento correcto cuando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) no es válido.
* Confirmar que los métodos generan el elemento <xref:Microsoft.AspNetCore.Mvc.IActionResult> correcto.
* Comprobar que las asignaciones de valores de propiedad se realizan correctamente.

Este grupo de pruebas suele simular los métodos de DAL para generar los datos esperados en el paso de acción, en el que un método de modelo de página se ejecuta. Por ejemplo, el método `GetMessagesAsync` de `AppDbContext` se simula para generar una salida. Cuando un método de modelo de página ejecuta este método, la simulación devuelve el resultado. Los datos no provienen de la base de datos. Esto crea condiciones de prueba de confianza y confiables para usar la DAL en las pruebas del modelo de página.

La prueba `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` muestra cómo se simula el método `GetMessagesAsync` para el modelo de página:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Cuando el método `OnGetAsync` se ejecuta en el paso de acción, llama al método `GetMessagesAsync` del modelo de página.

Paso de acción de prueba unitaria (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Método `OnGetAsync` del modelo de página `IndexPage` (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

El método `GetMessagesAsync` de la DAL no devuelve el resultado de esta llamada al método. La versión simulada del método devuelve el resultado.

En el paso `Assert`, los mensajes reales (`actualMessages`) se asignan desde la propiedad `Messages` del modelo de página. También se realiza una comprobación de tipo cuando los mensajes se asignan. Las propiedades `Text` de los mensajes esperados y reales se comparan. La prueba afirma que las dos instancias de `List<Message>` contienen los mismos mensajes.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Otras pruebas de este grupo crean objetos de modelo de página que incluyen <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, un elemento <xref:Microsoft.AspNetCore.Mvc.ActionContext> para establecer `PageContext`, un elemento `ViewDataDictionary` y un elemento `PageContext`. Todos ellos resultan útiles para realizar pruebas. Por ejemplo, la aplicación de mensajes establece un error de `ModelState` con <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> para comprobar si se devuelve un elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> válido cuando `OnPostAddMessageAsync` se ejecuta:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionales

* [Prueba unitaria de C# en .NET Core mediante pruebas de dotnet y xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Prueba unitaria del código](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Creación de una solución completa de .NET Core en macOS con Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introducción a xUnit.net: Uso de .NET Core con la línea de comandos del SDK de .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Inicio rápido de Moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core admite pruebas unitarias de aplicaciones de Razor Pages. Las pruebas de la capa de acceso a datos (DAL) y de los modelos de página ayudan a garantizar lo siguiente:

* Las partes de una aplicación de Razor Pages funcionan de forma independiente y conjunta como una unidad durante la creación de una aplicación.
* Las clases y los métodos tienen ámbitos de responsabilidad restringidos.
* Hay documentación adicional disponible sobre cómo debe comportarse la aplicación.
* Se detectan regresiones —que son errores que salen a la luz a raíz de las actualizaciones de código— en los procesos de compilación e implementación automatizados.

En este tema se da por hecho que el usuario posee conocimientos básicos de las pruebas unitarias y las aplicaciones de Razor Pages. Si no está familiarizado con los conceptos de prueba o aplicaciones de Razor Pages, vea los siguientes temas:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Prueba unitaria de C# en .NET Core mediante pruebas de dotnet y xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

El proyecto de ejemplo se compone de dos aplicaciones:

| Aplicación         | Carpeta del proyecto                     | Descripción |
| ----------- | ---------------------------------- | ----------- |
| Aplicación de mensajes | *src/RazorPagesTestSample*         | Permite a un usuario agregar un mensaje, eliminar un mensaje, eliminar todos los mensajes y analizar mensajes (hallar la media de palabras por mensaje). |
| Probar la aplicación    | *tests/RazorPagesTestSample.Tests* | Sirve para realizar una prueba unitaria del modelo de página Index y la DAL de la aplicación de mensajes. |

Las pruebas se pueden ejecutar con las características de prueba integradas de un IDE, como [Visual Studio](/visualstudio/test/unit-test-your-code) o [Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Si usa [Visual Studio Code](https://code.visualstudio.com/) o la línea de comandos, ejecute el siguiente comando en un símbolo del sistema en la carpeta *tests/RazorPagesTestSample.Tests*:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organización de la aplicación de mensajes

La aplicación de mensajes es un sistema de mensajes de Razor Pages con las siguientes características:

* La página Index de la aplicación (*Pages/Index.cshtml* y *Pages/Index.cshtml.cs*) proporciona una interfaz de usuario y métodos de modelo de página para controlar la adición, eliminación y análisis de mensajes (hallar la media de palabras por mensaje).
* La clase `Message` (*Data/Message.cs*) describe un mensaje con dos propiedades: `Id` (clave) y `Text` (mensaje). Se necesita la propiedad `Text`, que está limitada a 200 caracteres.
* Los mensajes se almacenan en la [base de datos en memoria de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* La aplicación contiene una DAL en su clase de contexto de base de datos, `AppDbContext` (*Data/AppDbContext.cs*). Los métodos de DAL se marcan como `virtual`, lo que permite realizar simulaciones en ellos para usarlos en las pruebas.
* Si la base de datos está vacía al inicio de una aplicación, el almacén de mensajes se inicializa con tres mensajes. Estos *mensajes inicializados* también se usan en las pruebas.

&#8224;En el tema de EF, [Pruebas con InMemory](/ef/core/miscellaneous/testing/in-memory), se explica cómo usar una base de datos en memoria con las pruebas con MSTest. En este tema se usa el marco de pruebas [xUnit](https://xunit.github.io/). Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.

Aunque la aplicación de ejemplo no usa el patrón del repositorio y no es un ejemplo eficaz del [patrón Unit of Work (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages admite estos patrones de desarrollo. Para más información, vea [Diseño del nivel de persistencia de infraestructura](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) y <xref:mvc/controllers/testing> (el ejemplo implementa el patrón del repositorio).

## <a name="test-app-organization"></a>Organización de la aplicación de prueba

La aplicación de prueba es una aplicación de consola dentro de la carpeta *tests/RazorPagesTestSample.Tests*.

| Carpeta de aplicación de prueba | Descripción |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* contiene las pruebas unitarias de la DAL.</li><li>*IndexPageTests.cs* contiene las pruebas unitarias del modelo de página Index.</li></ul> |
| *Utilities*     | Contiene el método `TestDbContextOptions` empleado para crear nuevas opciones de contexto de base de datos para cada prueba unitaria de DAL, de modo que la base de datos se restablezca a su condición de línea base en cada prueba. |

El marco de pruebas es [xUnit](https://xunit.github.io/). El marco de trabajo de simulación de objetos es [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Pruebas unitarias de la capa de acceso a datos (DAL)

La aplicación de mensajes tiene una DAL con cuatro métodos contenidos en la clase `AppDbContext` (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Cada método tiene una o dos pruebas unitarias en la aplicación de prueba.

| Método de DAL               | Función                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Obtiene un elemento `List<Message>` de la base de datos ordenada por la propiedad `Text`. |
| `AddMessageAsync`        | Agrega un elemento `Message` a la base de datos.                                          |
| `DeleteAllMessagesAsync` | Elimina todas las entradas `Message` de la base de datos.                           |
| `DeleteMessageAsync`     | Elimina todas las entradas `Message` de la base de datos según `Id`.                      |

Las pruebas unitarias de la DAL requieren <xref:Microsoft.EntityFrameworkCore.DbContextOptions> al crear un elemento `AppDbContext` para cada prueba. Un método para crear el elemento `DbContextOptions` de cada prueba es usar un elemento <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

El inconveniente de este método es que cada prueba recibe la base de datos en el estado en el que la dejó la prueba anterior. Esto puede ser problemático al intentar escribir pruebas unitarias atómicas que no interfieran entre sí. Para forzar que `AppDbContext` use un nuevo contexto de base de datos en cada prueba, proporcione una instancia de `DbContextOptions` que esté basada en un nuevo proveedor de servicios. La aplicación de prueba muestra cómo llevar esto a cabo usando el método `TestDbContextOptions` de su clase `Utilities` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

El uso de `DbContextOptions` en las pruebas unitarias de DAL permite que cada prueba se ejecute de forma atómica con una instancia de base de datos completamente nueva:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Cada método de prueba de la clase `DataAccessLayerTest` (*UnitTests/DataAccessLayerTest.cs*) sigue un patrón Organización-Acción-Aserción similar:

1. Organización: la base de datos se configura para la prueba y/o el resultado previsto se define.
1. Acción: la prueba se ejecuta.
1. Aserción: se realizan aserciones para determinar si el resultado de la prueba es correcto.

Por ejemplo, el método `DeleteMessageAsync` es responsable de quitar un mensaje individual identificado por su `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Hay dos pruebas para este método: una comprueba que el método elimina un mensaje cuando el mensaje está presente en la base de datos y la otra, que la base de datos no cambia si el `Id` del mensaje que se quiere eliminar no existe. Aquí mostramos el método `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

En primer lugar, el método lleva a cabo el paso de organización, donde se prepara todo para el paso de acción. Los mensajes de inicialización se obtienen y se conservan en `seedMessages`. Los mensajes de inicialización se guardan en la base de datos. El mensaje con un `Id` de `1` se establece para eliminarse. Cuando el método `DeleteMessageAsync` se ejecuta, los mensajes esperados deben incluir todos los mensajes, excepto el que tenga un `Id` de `1`. La variable `expectedMessages` representa el resultado esperado.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

El método actúa: El método `DeleteMessageAsync` se ejecuta pasando un `recId` de `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Por último, el método obtiene el elemento `Messages` del contexto y lo compara con el elemento `expectedMessages`, afirmando que los dos son iguales:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Para comparar que los dos elementos `List<Message>` son iguales:

* Los mensajes se ordenan por `Id`.
* Los pares de mensajes se comparan en la propiedad `Text`.

Un método de prueba similar, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`, comprueba el resultado de intentar eliminar un mensaje que no existe. En este caso, los mensajes esperados en la base de datos deben ser iguales a los mensajes reales después de que el método `DeleteMessageAsync` se ejecute. No debe haber ningún cambio en el contenido de la base de datos:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Pruebas unitarias de los métodos del modelo de página

Otro conjunto de pruebas unitarias es responsable de las pruebas de los métodos del modelo de página. En la aplicación de mensajes, los modelos de página Index se encuentran en la clase `IndexModel` en *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Método del modelo de página | Función |
| ----------------- | -------- |
| `OnGetAsync` | Obtiene los mensajes de la DAL de la interfaz de usuario mediante el método `GetMessagesAsync`. |
| `OnPostAddMessageAsync` | Si [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) es válido, se llama a `AddMessageAsync` para agregar un mensaje a la base de datos. |
| `OnPostDeleteAllMessagesAsync` | Llama a `DeleteAllMessagesAsync` para eliminar todos los mensajes de la base de datos. |
| `OnPostDeleteMessageAsync` | Ejecuta `DeleteMessageAsync` para eliminar un mensaje con el `Id` especificado. |
| `OnPostAnalyzeMessagesAsync` | Si hay uno o más mensajes en la base de datos, calcula la media de palabras por mensaje. |

Los métodos del modelo de página se comprueban con siete pruebas en la clase `IndexPageTests` (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Las pruebas usan el conocido patrón Organización-Aserción-Acción. Estas pruebas se centran en lo siguiente:

* Determinar si los métodos siguen el comportamiento correcto cuando [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) no es válido.
* Confirmar que los métodos generan el elemento <xref:Microsoft.AspNetCore.Mvc.IActionResult> correcto.
* Comprobar que las asignaciones de valores de propiedad se realizan correctamente.

Este grupo de pruebas suele simular los métodos de DAL para generar los datos esperados en el paso de acción, en el que un método de modelo de página se ejecuta. Por ejemplo, el método `GetMessagesAsync` de `AppDbContext` se simula para generar una salida. Cuando un método de modelo de página ejecuta este método, la simulación devuelve el resultado. Los datos no provienen de la base de datos. Esto crea condiciones de prueba de confianza y confiables para usar la DAL en las pruebas del modelo de página.

La prueba `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` muestra cómo se simula el método `GetMessagesAsync` para el modelo de página:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Cuando el método `OnGetAsync` se ejecuta en el paso de acción, llama al método `GetMessagesAsync` del modelo de página.

Paso de acción de prueba unitaria (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

Método `OnGetAsync` del modelo de página `IndexPage` (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

El método `GetMessagesAsync` de la DAL no devuelve el resultado de esta llamada al método. La versión simulada del método devuelve el resultado.

En el paso `Assert`, los mensajes reales (`actualMessages`) se asignan desde la propiedad `Messages` del modelo de página. También se realiza una comprobación de tipo cuando los mensajes se asignan. Las propiedades `Text` de los mensajes esperados y reales se comparan. La prueba afirma que las dos instancias de `List<Message>` contienen los mismos mensajes.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Otras pruebas de este grupo crean objetos de modelo de página que incluyen <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, un elemento <xref:Microsoft.AspNetCore.Mvc.ActionContext> para establecer `PageContext`, un elemento `ViewDataDictionary` y un elemento `PageContext`. Todos ellos resultan útiles para realizar pruebas. Por ejemplo, la aplicación de mensajes establece un error de `ModelState` con <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> para comprobar si se devuelve un elemento <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> válido cuando `OnPostAddMessageAsync` se ejecuta:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Recursos adicionales

* [Prueba unitaria de C# en .NET Core mediante pruebas de dotnet y xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Prueba unitaria del código](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Creación de una solución completa de .NET Core en macOS con Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Introducción a xUnit.net: Uso de .NET Core con la línea de comandos del SDK de .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Inicio rápido de Moq](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite): marco ficticio para desarrolladores de .NET. (*Microsoft no realiza su mantenimiento ni su soporte técnico.* )

::: moniker-end
