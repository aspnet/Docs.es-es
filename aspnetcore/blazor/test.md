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
ms.openlocfilehash: 572b9a293e2fd6f51431cd1de6ada737addf5efa
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746538"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="df3bd-103">Prueba de componentes en Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="df3bd-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="df3bd-104">Egil Hansen</span><span class="sxs-lookup"><span data-stu-id="df3bd-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="df3bd-105">Las pruebas son un aspecto importante de la creación de software estable y fácil de mantener.</span><span class="sxs-lookup"><span data-stu-id="df3bd-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="df3bd-106">Para probar un componente de Blazor, el *componente en prueba* (CUT):</span><span class="sxs-lookup"><span data-stu-id="df3bd-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="df3bd-107">Se representa con la entrada pertinente para la prueba.</span><span class="sxs-lookup"><span data-stu-id="df3bd-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="df3bd-108">Depende del tipo de prueba que se realice, posiblemente sujeto a interacción o modificación.</span><span class="sxs-lookup"><span data-stu-id="df3bd-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="df3bd-109">Por ejemplo, se pueden desencadenar controladores de eventos, como un evento `onclick` para un botón.</span><span class="sxs-lookup"><span data-stu-id="df3bd-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="df3bd-110">Se inspecciona en busca de los valores esperados.</span><span class="sxs-lookup"><span data-stu-id="df3bd-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="df3bd-111">Enfoques de prueba</span><span class="sxs-lookup"><span data-stu-id="df3bd-111">Test approaches</span></span>

<span data-ttu-id="df3bd-112">Dos enfoques habituales para probar componentes de Blazor son las pruebas de un extremo a otro (E2E) y las pruebas unitarias:</span><span class="sxs-lookup"><span data-stu-id="df3bd-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="df3bd-113">**Pruebas unitarias**: las [pruebas unitarias](/dotnet/core/testing/) se escriben con una biblioteca de pruebas unitarias que proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="df3bd-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="df3bd-114">Representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="df3bd-114">Component rendering.</span></span>
  * <span data-ttu-id="df3bd-115">Inspección del estado y la salida del componente.</span><span class="sxs-lookup"><span data-stu-id="df3bd-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="df3bd-116">Desencadenamiento de controladores de eventos y métodos de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="df3bd-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="df3bd-117">Aserciones del comportamiento correcto del componente.</span><span class="sxs-lookup"><span data-stu-id="df3bd-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="df3bd-118">[bUnit](https://github.com/egil/bUnit) es un ejemplo de una biblioteca que permite realizar pruebas unitarias de componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="df3bd-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="df3bd-119">**Pruebas de E2E**: un ejecutor de pruebas ejecuta una aplicación Blazor que contiene el CUT y automatiza una instancia del explorador.</span><span class="sxs-lookup"><span data-stu-id="df3bd-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="df3bd-120">La herramienta de pruebas inspecciona el CUT e interactúa con él a través del explorador.</span><span class="sxs-lookup"><span data-stu-id="df3bd-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="df3bd-121">[Selenium](https://github.com/SeleniumHQ/selenium) es un ejemplo de un marco de pruebas de E2E que se puede usar con aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="df3bd-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="df3bd-122">En las pruebas unitarias, solo está implicado el componente de Blazor (Razor/C#).</span><span class="sxs-lookup"><span data-stu-id="df3bd-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="df3bd-123">Las dependencias externas, como los servicios y la interoperabilidad de JS, deben ser ficticias.</span><span class="sxs-lookup"><span data-stu-id="df3bd-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="df3bd-124">En las pruebas de E2E, el componente de Blazor y toda su infraestructura auxiliar forman parte de la prueba, incluidos CSS, JS y el DOM y las API del explorador.</span><span class="sxs-lookup"><span data-stu-id="df3bd-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="df3bd-125">El *ámbito de la prueba* describe el alcance de las pruebas.</span><span class="sxs-lookup"><span data-stu-id="df3bd-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="df3bd-126">El ámbito de la prueba suele influir en la velocidad de las pruebas.</span><span class="sxs-lookup"><span data-stu-id="df3bd-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="df3bd-127">Las pruebas unitarias se ejecutan en un subconjunto de los subsistemas de la aplicación, normalmente en cuestión de milisegundos.</span><span class="sxs-lookup"><span data-stu-id="df3bd-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="df3bd-128">Las pruebas de E2E, que prueban un grupo amplio de los subsistemas de la aplicación, pueden tardar varios segundos en completarse.</span><span class="sxs-lookup"><span data-stu-id="df3bd-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="df3bd-129">Las pruebas unitarias también proporcionan acceso a la instancia del CUT, lo que permite inspeccionar y comprobar el estado interno del componente.</span><span class="sxs-lookup"><span data-stu-id="df3bd-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="df3bd-130">Normalmente, esto no es posible en las pruebas de E2E.</span><span class="sxs-lookup"><span data-stu-id="df3bd-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="df3bd-131">Con respecto al entorno del componente, las pruebas de E2E deben asegurarse de que se ha alcanzado el estado del entorno esperado antes de que se inicie la comprobación.</span><span class="sxs-lookup"><span data-stu-id="df3bd-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="df3bd-132">De lo contrario, el resultado es impredecible.</span><span class="sxs-lookup"><span data-stu-id="df3bd-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="df3bd-133">En las pruebas unitarias, la representación del CUT y del ciclo de vida de la prueba están más integrados, lo que mejora la estabilidad de la prueba.</span><span class="sxs-lookup"><span data-stu-id="df3bd-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="df3bd-134">Las pruebas de E2E implican el inicio de varios procesos, la E/S de red y de disco, y otras actividades del subsistema que, a menudo, generan una mala confiabilidad de las pruebas.</span><span class="sxs-lookup"><span data-stu-id="df3bd-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="df3bd-135">Normalmente, las pruebas unitarias se aíslan de estos tipos de problemas.</span><span class="sxs-lookup"><span data-stu-id="df3bd-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="df3bd-136">En la tabla siguiente se resumen las diferencias entre los dos enfoques de prueba.</span><span class="sxs-lookup"><span data-stu-id="df3bd-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="df3bd-137">Capacidad</span><span class="sxs-lookup"><span data-stu-id="df3bd-137">Capability</span></span>                       | <span data-ttu-id="df3bd-138">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="df3bd-138">Unit testing</span></span>                     | <span data-ttu-id="df3bd-139">Pruebas de E2E</span><span class="sxs-lookup"><span data-stu-id="df3bd-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="df3bd-140">Ámbito de la prueba</span><span class="sxs-lookup"><span data-stu-id="df3bd-140">Test scope</span></span>                       | <span data-ttu-id="df3bd-141">Solo el componente de Blazor (Razor/C#)</span><span class="sxs-lookup"><span data-stu-id="df3bd-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="df3bd-142">El componente de Blazor (Razor/C#) con CSS/JS</span><span class="sxs-lookup"><span data-stu-id="df3bd-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="df3bd-143">Tiempo de ejecución de la prueba</span><span class="sxs-lookup"><span data-stu-id="df3bd-143">Test execution time</span></span>              | <span data-ttu-id="df3bd-144">Milisegundos</span><span class="sxs-lookup"><span data-stu-id="df3bd-144">Milliseconds</span></span>                     | <span data-ttu-id="df3bd-145">Segundos</span><span class="sxs-lookup"><span data-stu-id="df3bd-145">Seconds</span></span>                                 |
| <span data-ttu-id="df3bd-146">Acceso a la instancia del componente</span><span class="sxs-lookup"><span data-stu-id="df3bd-146">Access to the component instance</span></span> | <span data-ttu-id="df3bd-147">Sí</span><span class="sxs-lookup"><span data-stu-id="df3bd-147">Yes</span></span>                              | <span data-ttu-id="df3bd-148">No</span><span class="sxs-lookup"><span data-stu-id="df3bd-148">No</span></span>                                      |
| <span data-ttu-id="df3bd-149">Sensible al entorno</span><span class="sxs-lookup"><span data-stu-id="df3bd-149">Sensitive to the environment</span></span>     | <span data-ttu-id="df3bd-150">No</span><span class="sxs-lookup"><span data-stu-id="df3bd-150">No</span></span>                               | <span data-ttu-id="df3bd-151">Sí</span><span class="sxs-lookup"><span data-stu-id="df3bd-151">Yes</span></span>                                     |
| <span data-ttu-id="df3bd-152">Confiabilidad</span><span class="sxs-lookup"><span data-stu-id="df3bd-152">Reliability</span></span>                      | <span data-ttu-id="df3bd-153">Más confiable</span><span class="sxs-lookup"><span data-stu-id="df3bd-153">More reliable</span></span>                    | <span data-ttu-id="df3bd-154">Menos confiable</span><span class="sxs-lookup"><span data-stu-id="df3bd-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="df3bd-155">Elección del enfoque de prueba más adecuado</span><span class="sxs-lookup"><span data-stu-id="df3bd-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="df3bd-156">A la hora de elegir el tipo de prueba que se va a realizar, tenga en cuenta el escenario.</span><span class="sxs-lookup"><span data-stu-id="df3bd-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="df3bd-157">En la tabla siguiente se describen algunas consideraciones.</span><span class="sxs-lookup"><span data-stu-id="df3bd-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="df3bd-158">Escenario</span><span class="sxs-lookup"><span data-stu-id="df3bd-158">Scenario</span></span> | <span data-ttu-id="df3bd-159">Enfoque sugerido</span><span class="sxs-lookup"><span data-stu-id="df3bd-159">Suggested approach</span></span> | <span data-ttu-id="df3bd-160">Observaciones</span><span class="sxs-lookup"><span data-stu-id="df3bd-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="df3bd-161">Componente sin lógica de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="df3bd-161">Component without JS interop logic</span></span> | <span data-ttu-id="df3bd-162">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="df3bd-162">Unit testing</span></span> | <span data-ttu-id="df3bd-163">Cuando no hay ninguna dependencia de la interoperabilidad de JS en un componente de Blazor, el componente se puede probar sin acceso a JS ni a la API de DOM.</span><span class="sxs-lookup"><span data-stu-id="df3bd-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="df3bd-164">En este escenario, la elección de pruebas unitarias no supone ninguna desventaja.</span><span class="sxs-lookup"><span data-stu-id="df3bd-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="df3bd-165">Componente con lógica de interoperabilidad de JS simple</span><span class="sxs-lookup"><span data-stu-id="df3bd-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="df3bd-166">Pruebas unitarias</span><span class="sxs-lookup"><span data-stu-id="df3bd-166">Unit testing</span></span> | <span data-ttu-id="df3bd-167">Es habitual que los componentes consulten las animaciones el DOM o que desencadenen animaciones través de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="df3bd-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="df3bd-168">En este escenario normalmente se prefieren las pruebas unitarias, ya que resulta sencillo simular la interacción de JS a través de la interfaz <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="df3bd-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="df3bd-169">Componente que depende de código de JS complejo</span><span class="sxs-lookup"><span data-stu-id="df3bd-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="df3bd-170">Pruebas unitarias y pruebas de JS independientes</span><span class="sxs-lookup"><span data-stu-id="df3bd-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="df3bd-171">Si un componente usa la interoperabilidad de JS para llamar a una biblioteca de JS grande o compleja, pero la interacción entre el componente de Blazor y la biblioteca de JS es sencilla, es probable que el mejor enfoque sea tratar el componente y la biblioteca o el código de JS como dos elementos independientes y probarlos de forma individual.</span><span class="sxs-lookup"><span data-stu-id="df3bd-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="df3bd-172">Pruebe el componente de Blazor con una biblioteca de pruebas unitarias y JS con una biblioteca de pruebas de JS.</span><span class="sxs-lookup"><span data-stu-id="df3bd-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="df3bd-173">Componente con lógica que depende de la manipulación de JS del DOM del explorador</span><span class="sxs-lookup"><span data-stu-id="df3bd-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="df3bd-174">Pruebas de E2E</span><span class="sxs-lookup"><span data-stu-id="df3bd-174">E2E testing</span></span> | <span data-ttu-id="df3bd-175">Cuando la funcionalidad de un componente depende de JS y su manipulación del DOM, compruebe JS y el código de Blazor conjuntamente en una prueba de E2E.</span><span class="sxs-lookup"><span data-stu-id="df3bd-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="df3bd-176">Este es el enfoque que los desarrolladores del marco Blazor han adoptado con la lógica de representación del explorador de Blazor, que tiene código de C# y JS estrechamente acoplado.</span><span class="sxs-lookup"><span data-stu-id="df3bd-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="df3bd-177">El código de C# y JS debe trabajar de forma conjunta para representar correctamente componentes de Blazor en un explorador.</span><span class="sxs-lookup"><span data-stu-id="df3bd-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="df3bd-178">Componente que depende de una biblioteca de componentes de terceros con dependencias difíciles de simular</span><span class="sxs-lookup"><span data-stu-id="df3bd-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="df3bd-179">Pruebas de E2E</span><span class="sxs-lookup"><span data-stu-id="df3bd-179">E2E testing</span></span> | <span data-ttu-id="df3bd-180">Cuando la funcionalidad de un componente depende de una biblioteca de componentes de terceros con dependencias difíciles de simular, como la interoperabilidad de JS, la prueba de E2E podría ser la única opción para probar el componente.</span><span class="sxs-lookup"><span data-stu-id="df3bd-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="df3bd-181">Prueba de componentes con bUnit</span><span class="sxs-lookup"><span data-stu-id="df3bd-181">Test components with bUnit</span></span>

<span data-ttu-id="df3bd-182">No hay ningún marco de pruebas oficial de Microsoft para Blazor, pero el proyecto controlado por la comunidad [bUnit](https://github.com/egil/bUnit) proporciona una manera cómoda de realizar pruebas unitarias de componentes de Blazor.</span><span class="sxs-lookup"><span data-stu-id="df3bd-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="df3bd-183">bUnit es una biblioteca de pruebas de terceros y no es compatible con Microsoft, que tampoco la mantiene.</span><span class="sxs-lookup"><span data-stu-id="df3bd-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="df3bd-184">bUnit funciona con marcos de pruebas de uso general, como [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/) y [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="df3bd-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="df3bd-185">Estos marcos de pruebas hacen que las pruebas de bUnit parezcan pruebas unitarias normales.</span><span class="sxs-lookup"><span data-stu-id="df3bd-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="df3bd-186">Las pruebas de bUnit integradas con un marco de pruebas de uso general normalmente se ejecutan con:</span><span class="sxs-lookup"><span data-stu-id="df3bd-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="df3bd-187">[Explorador de pruebas de Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="df3bd-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="df3bd-188">El comando [`dotnet test`](/dotnet/core/tools/dotnet-test) de la CLI en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="df3bd-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="df3bd-189">Una canalización de prueba de DevOps automatizada.</span><span class="sxs-lookup"><span data-stu-id="df3bd-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="df3bd-190">Los conceptos y las implementaciones de prueba de diferentes marcos de pruebas son similares, pero no idénticos.</span><span class="sxs-lookup"><span data-stu-id="df3bd-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="df3bd-191">Consulte la documentación del marco de pruebas para obtener instrucciones.</span><span class="sxs-lookup"><span data-stu-id="df3bd-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="df3bd-192">A continuación se muestra la estructura de una prueba de bUnit en el componente `Counter` en una aplicación basada en una plantilla de proyecto de Blazor.</span><span class="sxs-lookup"><span data-stu-id="df3bd-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="df3bd-193">El componente `Counter` muestra e incrementa un contador en función de la selección de un botón en la página por parte del usuario:</span><span class="sxs-lookup"><span data-stu-id="df3bd-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

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

<span data-ttu-id="df3bd-194">La prueba de bUnit siguiente comprueba que el contador del CUT se incrementa correctamente cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="df3bd-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="df3bd-195">En cada paso de la prueba tienen lugar las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="df3bd-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="df3bd-196">*Organización*: el componente `Counter` se representa mediante el objeto `TestContext` de bUnit.</span><span class="sxs-lookup"><span data-stu-id="df3bd-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="df3bd-197">Se encuentra el elemento de párrafo del CUT (`<p>`) y se asigna a `paraElm`.</span><span class="sxs-lookup"><span data-stu-id="df3bd-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="df3bd-198">*Acción*: se encuentra el elemento de botón (`<button>`) y, después, se selecciona mediante una llamada a `Click`, que debe incrementar el contador y actualizar el contenido de la etiqueta de párrafo (`<p>`).</span><span class="sxs-lookup"><span data-stu-id="df3bd-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="df3bd-199">El contenido textual del elemento de párrafo se obtiene mediante una llamada a `TextContent`.</span><span class="sxs-lookup"><span data-stu-id="df3bd-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="df3bd-200">*Aserción*: se llama a `MarkupMatches` en el contenido textual para comprobar que coincide con la cadena esperada, que es `Current count: 1`.</span><span class="sxs-lookup"><span data-stu-id="df3bd-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="df3bd-201">El método de aserción `MarkupMatches` difiere de una aserción de comparación de cadenas convencional (por ejemplo, `Assert.Equal("Current count: 1", paraElmText);`); `MarkupMatches` realiza una comparación semántica de la entrada y el marcado HTML esperado.</span><span class="sxs-lookup"><span data-stu-id="df3bd-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="df3bd-202">Una comparación semántica tiene en cuenta la semántica de HTML, lo que significa que se omiten elementos como un espacio en blanco insignificante.</span><span class="sxs-lookup"><span data-stu-id="df3bd-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="df3bd-203">Esto genera pruebas más estables.</span><span class="sxs-lookup"><span data-stu-id="df3bd-203">This results in more stable tests.</span></span> <span data-ttu-id="df3bd-204">Para obtener más información, vea [Personalización de la comparación HTML semántica](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="df3bd-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df3bd-205">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="df3bd-205">Additional resources</span></span>

* <span data-ttu-id="df3bd-206">[Introducción a bUnit](https://bunit.egilhansen.com/docs/getting-started/): las instrucciones de bUnit se aplican a la creación de un proyecto de prueba, hacer referencia a los paquetes del marco de pruebas y la compilación y ejecución de pruebas.</span><span class="sxs-lookup"><span data-stu-id="df3bd-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
