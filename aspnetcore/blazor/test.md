---
title: Prueba de componentes en Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo probar componentes en aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 8a6fa8f25c8209584488fb2578c70e884877d666
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625874"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Prueba de componentes en Blazor de ASP.NET Core

[Egil Hansen](https://egilhansen.com/)

Las pruebas son un aspecto importante de la creación de software estable y fácil de mantener.

Para probar un componente de Blazor, el *componente en prueba* (CUT):

* Se representa con la entrada pertinente para la prueba.
* Depende del tipo de prueba que se realice, posiblemente sujeto a interacción o modificación. Por ejemplo, se pueden desencadenar controladores de eventos, como un evento `onclick` para un botón.
* Se inspecciona en busca de los valores esperados.

## <a name="test-approaches"></a>Enfoques de prueba

Dos enfoques habituales para probar componentes de Blazor son las pruebas de un extremo a otro (E2E) y las pruebas unitarias:

* **Pruebas unitarias**: las [pruebas unitarias](/dotnet/core/testing/) se escriben con una biblioteca de pruebas unitarias que proporciona lo siguiente:
  * Representación de componentes.
  * Inspección del estado y la salida del componente.
  * Desencadenamiento de controladores de eventos y métodos de ciclo de vida.
  * Aserciones del comportamiento correcto del componente.

  [bUnit](https://github.com/egil/bUnit) es un ejemplo de una biblioteca que permite realizar pruebas unitarias de componentes de Razor.

* **Pruebas de E2E**: un ejecutor de pruebas ejecuta una aplicación Blazor que contiene el CUT y automatiza una instancia del explorador. La herramienta de pruebas inspecciona el CUT e interactúa con él a través del explorador. [Selenium](https://github.com/SeleniumHQ/selenium) es un ejemplo de un marco de pruebas de E2E que se puede usar con aplicaciones Blazor.

En las pruebas unitarias, solo está implicado el componente de Blazor (Razor/C#). Las dependencias externas, como los servicios y la interoperabilidad de JS, deben ser ficticias. En las pruebas de E2E, el componente de Blazor y toda su infraestructura auxiliar forman parte de la prueba, incluidos CSS, JS y el DOM y las API del explorador.

El *ámbito de la prueba* describe el alcance de las pruebas. El ámbito de la prueba suele influir en la velocidad de las pruebas. Las pruebas unitarias se ejecutan en un subconjunto de los subsistemas de la aplicación, normalmente en cuestión de milisegundos. Las pruebas de E2E, que prueban un grupo amplio de los subsistemas de la aplicación, pueden tardar varios segundos en completarse. 

Las pruebas unitarias también proporcionan acceso a la instancia del CUT, lo que permite inspeccionar y comprobar el estado interno del componente. Normalmente, esto no es posible en las pruebas de E2E.

Con respecto al entorno del componente, las pruebas de E2E deben asegurarse de que se ha alcanzado el estado del entorno esperado antes de que se inicie la comprobación. De lo contrario, el resultado es impredecible. En las pruebas unitarias, la representación del CUT y del ciclo de vida de la prueba están más integrados, lo que mejora la estabilidad de la prueba.

Las pruebas de E2E implican el inicio de varios procesos, la E/S de red y de disco, y otras actividades del subsistema que, a menudo, generan una mala confiabilidad de las pruebas. Normalmente, las pruebas unitarias se aíslan de estos tipos de problemas.

En la tabla siguiente se resumen las diferencias entre los dos enfoques de prueba.

| Capacidad                       | Pruebas unitarias                     | Pruebas de E2E                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Ámbito de la prueba                       | Solo el componente de Blazor (Razor/C#) | El componente de Blazor (Razor/C#) con CSS/JS |
| Tiempo de ejecución de la prueba              | Milisegundos                     | Segundos                                 |
| Acceso a la instancia del componente | Sí                              | No                                      |
| Sensible al entorno     | No                               | Sí                                     |
| Confiabilidad                      | Más confiable                    | Menos confiable                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Elección del enfoque de prueba más adecuado

A la hora de elegir el tipo de prueba que se va a realizar, tenga en cuenta el escenario. En la tabla siguiente se describen algunas consideraciones.

| Escenario | Enfoque sugerido | Observaciones |
| -------- | ------------------ | ------- |
| Componente sin lógica de interoperabilidad de JS | Pruebas unitarias | Cuando no hay ninguna dependencia de la interoperabilidad de JS en un componente de Blazor, el componente se puede probar sin acceso a JS ni a la API de DOM. En este escenario, la elección de pruebas unitarias no supone ninguna desventaja. |
| Componente con lógica de interoperabilidad de JS simple | Pruebas unitarias | Es habitual que los componentes consulten las animaciones el DOM o que desencadenen animaciones través de la interoperabilidad de JS. En este escenario normalmente se prefieren las pruebas unitarias, ya que resulta sencillo simular la interacción de JS a través de la interfaz <xref:Microsoft.JSInterop.IJSRuntime>. |
| Componente que depende de código de JS complejo | Pruebas unitarias y pruebas de JS independientes | Si un componente usa la interoperabilidad de JS para llamar a una biblioteca de JS grande o compleja, pero la interacción entre el componente de Blazor y la biblioteca de JS es sencilla, es probable que el mejor enfoque sea tratar el componente y la biblioteca o el código de JS como dos elementos independientes y probarlos de forma individual. Pruebe el componente de Blazor con una biblioteca de pruebas unitarias y JS con una biblioteca de pruebas de JS. |
| Componente con lógica que depende de la manipulación de JS del DOM del explorador | Pruebas de E2E | Cuando la funcionalidad de un componente depende de JS y su manipulación del DOM, compruebe JS y el código de Blazor conjuntamente en una prueba de E2E. Este es el enfoque que los desarrolladores del marco Blazor han adoptado con la lógica de representación del explorador de Blazor, que tiene código de C# y JS estrechamente acoplado. El código de C# y JS debe trabajar de forma conjunta para representar correctamente componentes de Blazor en un explorador.
| Componente que depende de una biblioteca de componentes de terceros con dependencias difíciles de simular | Pruebas de E2E | Cuando la funcionalidad de un componente depende de una biblioteca de componentes de terceros con dependencias difíciles de simular, como la interoperabilidad de JS, la prueba de E2E podría ser la única opción para probar el componente. |

## <a name="test-components-with-bunit"></a>Prueba de componentes con bUnit

No hay ningún marco de pruebas oficial de Microsoft para Blazor, pero el proyecto controlado por la comunidad [bUnit](https://github.com/egil/bUnit) proporciona una manera cómoda de realizar pruebas unitarias de componentes de Blazor.

> [!NOTE]
> bUnit es una biblioteca de pruebas de terceros y no es compatible con Microsoft, que tampoco la mantiene.

bUnit funciona con marcos de pruebas de uso general, como [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/) y [xUnit](https://xunit.github.io/). Estos marcos de pruebas hacen que las pruebas de bUnit parezcan pruebas unitarias normales. Las pruebas de bUnit integradas con un marco de pruebas de uso general normalmente se ejecutan con:

* [Explorador de pruebas de Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* El comando [`dotnet test`](/dotnet/core/tools/dotnet-test) de la CLI en un shell de comandos.
* Una canalización de prueba de DevOps automatizada.

> [!NOTE]
> Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos. Consulte la documentación del marco de pruebas para obtener instrucciones.

A continuación se muestra la estructura de una prueba de bUnit en el componente `Counter` en una aplicación basada en una plantilla de proyecto de Blazor. El componente `Counter` muestra e incrementa un contador en función de la selección de un botón en la página por parte del usuario:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

La prueba de bUnit siguiente comprueba que el contador del CUT se incrementa correctamente cuando se selecciona el botón:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

En cada paso de la prueba tienen lugar las acciones siguientes:

* *Organización*: el componente `Counter` se representa mediante el objeto `TestContext` de bUnit. Se encuentra el elemento de párrafo del CUT (`<p>`) y se asigna a `paraElm`.

* *Acción*: se encuentra el elemento de botón (`<button>`) y, después, se selecciona mediante una llamada a `Click`, que debe incrementar el contador y actualizar el contenido de la etiqueta de párrafo (`<p>`). El contenido textual del elemento de párrafo se obtiene mediante una llamada a `TextContent`.

* *Aserción*: se llama a `MarkupMatches` en el contenido textual para comprobar que coincide con la cadena esperada, que es `Current count: 1`.

> [!NOTE]
> El método de aserción `MarkupMatches` difiere de una aserción de comparación de cadenas convencional (por ejemplo, `Assert.Equal("Current count: 1", paraElmText);`); `MarkupMatches` realiza una comparación semántica de la entrada y el marcado HTML esperado. Una comparación semántica tiene en cuenta la semántica de HTML, lo que significa que se omiten elementos como un espacio en blanco insignificante. Esto genera pruebas más estables. Para obtener más información, vea [Personalización de la comparación HTML semántica](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción a bUnit](https://bunit.egilhansen.com/docs/getting-started/): las instrucciones de bUnit se aplican a la creación de un proyecto de prueba, hacer referencia a los paquetes del marco de pruebas y la compilación y ejecución de pruebas.
