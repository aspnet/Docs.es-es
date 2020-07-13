---
title: Control de errores en aplicaciones Blazor de ASP.NET Core
author: guardrex
description: Descubra cómo Blazor de ASP.NET Core administra las excepciones no controladas y cómo desarrollar aplicaciones que detecten y controlen los errores.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: e3ce3a62f351255fd059adaa6e9b0a8e9bdc2ce7
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059882"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Control de errores en aplicaciones Blazor de ASP.NET Core

Por [Steve Sanderson](https://github.com/SteveSandersonMS)

En este artículo se describe cómo Blazor administra las excepciones no controladas y cómo desarrollar aplicaciones que detecten y controlen los errores.

## <a name="detailed-errors-during-development"></a>Errores detallados durante el desarrollo

Cuando una aplicación Blazor no funciona correctamente durante el desarrollo, recibir información detallada del error de la aplicación ayuda a solucionar el problema. Cuando se produce un error, en las aplicaciones Blazor se muestra una barra dorada en la parte inferior de la pantalla:

* Durante el desarrollo, la barra dorada le dirige a la consola del explorador, donde puede ver la excepción.
* En producción, la barra dorada informa al usuario de que se ha producido un error y recomienda actualizar el explorador.

La interfaz de usuario para esta experiencia de control de errores forma parte de las plantillas de proyecto de Blazor.

En una aplicación Blazor WebAssembly, personalice la experiencia en el archivo `wwwroot/index.html`:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

En una aplicación Blazor Server, personalice la experiencia en el archivo `Pages/_Host.cshtml`:

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Los estilos incluidos en las plantillas de Blazor (`wwwroot/css/app.css` o `wwwroot/css/site.css`) ocultan el elemento `blazor-error-ui`, que se muestra posteriormente cuando se produce un error:

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Cómo reacciona una aplicación Blazor Server a las excepciones no controladas

Blazor Server es un marco con estado. Mientras los usuarios interactúan con una aplicación, mantienen una conexión con el servidor, lo que se denomina *circuito*. El circuito contiene instancias de componentes activas, además de muchos otros aspectos del estado, como:

* La salida representada más reciente de los componentes.
* El conjunto actual de delegados de control de eventos que se pueden desencadenar por eventos del lado cliente.

Si un usuario abre la aplicación en varias pestañas del explorador, tiene varios circuitos independientes.

Blazor trata la mayoría de las excepciones no controladas como graves para el circuito en el que se producen. Si se finaliza un circuito debido a una excepción no controlada, el usuario solo puede seguir interactuando con la aplicación si recarga la página para crear otro circuito. Los circuitos externos al que se ha finalizado, que son circuitos para otros usuarios u otras pestañas del explorador, no se ven afectados. Este escenario es parecido a cuando una aplicación de escritorio se bloquea. La aplicación bloqueada debe reiniciarse, pero otras no resultan afectadas.

Un circuito finaliza cuando se produce una excepción no controlada por los siguientes motivos:

* Una excepción no controlada a menudo deja el circuito en un estado indefinido.
* Después de una excepción no controlada no se puede garantizar el funcionamiento normal de la aplicación.
* Es posible que aparezcan vulnerabilidades de seguridad en la aplicación si el circuito continúa.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Administración de excepciones no controladas en el código de desarrollador

Para que una aplicación continúe después de un error, debe tener lógica de control de errores. En secciones posteriores de este artículo se describen posibles orígenes de excepciones no controladas.

En producción, en la interfaz de usuario no se representan mensajes de excepción del marco ni seguimientos de la pila. La representación de mensajes de excepción o seguimientos de la pila podría:

* Divulgar información confidencial a los usuarios finales.
* Ayudar a un usuario malintencionado a detectar debilidades en una aplicación que pueden poner en peligro la seguridad de la aplicación, el servidor o la red.

## <a name="log-errors-with-a-persistent-provider"></a>Registro de errores con un proveedor persistente

Si se produce una excepción no controlada, la excepción se registra en las instancias de <xref:Microsoft.Extensions.Logging.ILogger> configuradas en el contenedor de servicios. De forma predeterminada, las aplicaciones Blazor registran en la salida de la consola con el proveedor de registro de la consola. Considere la posibilidad de realizar el registro en una ubicación más permanente con un proveedor que administre el tamaño y la rotación del registro. Para obtener más información, vea <xref:fundamentals/logging/index>.

Durante el desarrollo, Blazor normalmente envía los detalles completos de las excepciones a la consola del explorador para ayudar en la depuración. En producción, los errores detallados en la consola del explorador están deshabilitados de forma predeterminada, lo que significa que los errores no se envían a los clientes, pero los detalles completos de la excepción se siguen registrando en el lado del servidor. Para obtener más información, vea <xref:fundamentals/error-handling>.

Debe decidir qué incidentes registrar y el nivel de gravedad de los incidentes registrados. Es posible que los usuarios hostiles puedan desencadenar errores deliberadamente. Por ejemplo, no registre un incidente de un error en el que se proporcione un valor `ProductId` desconocido en la dirección URL de un componente que muestra los detalles del producto. No todos los errores se deben tratar como incidentes de alta gravedad para el registro.

Para obtener más información, vea <xref:blazor/fundamentals/logging>.

## <a name="places-where-errors-may-occur"></a>Lugares donde se pueden producir errores

El código del marco de trabajo y la aplicación puede desencadenar excepciones no controladas en cualquiera de las ubicaciones siguientes:

* [Creación de instancias de componentes](#component-instantiation)
* [Métodos de ciclo de vida](#lifecycle-methods)
* [Lógica de representación](#rendering-logic)
* [Controladores de eventos](#event-handlers)
* [Eliminación de componentes](#component-disposal)
* [Interoperabilidad de JavaScript](#javascript-interop)
* [Nueva representación de Blazor Server](#blazor-server-prerendering)

Las excepciones no controladas anteriores se describen en las siguientes secciones de este artículo.

### <a name="component-instantiation"></a>Creación de instancias de componentes

Cuando Blazor crea una instancia de un componente:

* Se invoca el constructor del componente.
* Se invocan los constructores de cualquier servicio de inserción de dependencias que no sea singleton proporcionado al constructor del componente a través de la directiva [`@inject`](xref:mvc/views/razor#inject) o el atributo [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component).

Se produce un error en un circuito de Blazor Server cuando cualquier constructor ejecutado o un establecedor de cualquier propiedad `[Inject]` inicia una excepción no controlada. La excepción es grave porque el marco no puede crear una instancia del componente. Si la lógica del constructor puede iniciar excepciones, la aplicación debe interceptarlas mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.

### <a name="lifecycle-methods"></a>Métodos de ciclo de vida

Durante la vigencia de un componente, Blazor invoca los [métodos de ciclo de vida](xref:blazor/components/lifecycle) siguientes:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

Si cualquier método de ciclo de vida inicia una excepción, de forma sincrónica o asincrónica, la excepción es grave para un circuito de Blazor Server. Para que los componentes traten los errores de los métodos de ciclo de vida, agregue lógica de control de errores.

En el ejemplo siguiente, donde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> llama a un método para obtener un producto:

* Una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) controla una excepción que se inicia en el método `ProductRepository.GetProductByIdAsync`.
* Cuando se ejecuta el bloque `catch`:
  * `loadFailed` se establece en `true`, que se usa para mostrar un mensaje de error al usuario.
  * El error no se registra.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Lógica de representación

El marcado declarativo de un archivo de componente `.razor` se compila en un método de C# denominado <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>. Cuando se representa un componente, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> ejecuta y genera una estructura de datos que describe los elementos, el texto y los componentes secundarios del componente representado.

La lógica de representación puede iniciar una excepción. Un ejemplo de este escenario se produce cuando se evalúa `@someObject.PropertyName` pero `@someObject` es `null`. Una excepción no controlada iniciada por la lógica de representación es grave para un circuito de Blazor Server.

Para evitar una excepción de referencia nula en la lógica de representación, busque un objeto `null` antes de acceder a sus miembros. En el ejemplo siguiente, no se accede a las propiedades `person.Address` si `person.Address` es `null`:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

En el código anterior se supone que `person` no es `null`. A menudo, la estructura del código garantiza que un objeto existe en el momento en que se representa el componente. En esos casos, no es necesario comprobar `null` en la lógica de representación. En el ejemplo anterior, es posible que se garantice la existencia de `person` porque se crea `person` cuando se crea una instancia del componente.

### <a name="event-handlers"></a>Controladores de eventos

El código del lado cliente desencadena invocaciones de código de C# cuando se crean controladores de eventos mediante:

* `@onclick`
* `@onchange`
* Otros atributos `@on...`
* `@bind`

Es posible que el código del controlador de eventos inicie una excepción no controlada en estos escenarios.

Si un controlador de eventos inicia una excepción no controlada (por ejemplo, se produce un error en una consulta de base de datos), la excepción es grave para un circuito de Blazor Server. Si la aplicación llama a código que podría generar un error por motivos externos, capture las excepciones mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.

Si el código de usuario no captura y controla la excepción, el marco de trabajo registra la excepción y finaliza el circuito.

### <a name="component-disposal"></a>Eliminación de componentes

Un componente se puede quitar de la interfaz de usuario, por ejemplo, porque el usuario ha navegado a otra página. Cuando un componente que implementa <xref:System.IDisposable?displayProperty=fullName> se quita de la interfaz de usuario, el marco de trabajo llama al método <xref:System.IDisposable.Dispose%2A> del componente.

Si el método `Dispose` del componente inicia una excepción no controlada, la excepción es grave para un circuito de Blazor Server. Si la lógica de eliminación puede iniciar excepciones, la aplicación debe interceptarlas mediante una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro.

Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

### <a name="javascript-interop"></a>Interoperabilidad de JavaScript

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> permite que el código de .NET realice llamadas asincrónicas al entorno de ejecución de JavaScript en el explorador del usuario.

Se aplican las condiciones siguientes al control de errores con <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:

* Si una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> produce un error de forma sincrónica, se produce una excepción de .NET. Se puede producir un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>, por ejemplo, porque no se puedan serializar los argumentos proporcionados. El código del desarrollador debe detectar la excepción. Si el código de la aplicación en un controlador de eventos o en un método de ciclo de vida de componente no controla una excepción, la excepción resultante es grave para un circuito de Blazor Server.
* Si se produce un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> de forma asincrónica, se produce un error en el objeto <xref:System.Threading.Tasks.Task> de .NET. Se puede producir un error en una llamada a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>, por ejemplo, porque el código de JavaScript inicia una excepción o devuelve un objeto `Promise` que se ha completado como `rejected`. El código del desarrollador debe detectar la excepción. Si usa el operador [`await`](/dotnet/csharp/language-reference/keywords/await), considere la posibilidad de encapsular la llamada de método en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro. De lo contrario, el código con error provoca una excepción no controlada que es grave para un circuito de Blazor Server.
* De forma predeterminada, las llamadas a <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> se deben completar en un período determinado o, de lo contrario, se agota el tiempo de espera de la llamada. El período de tiempo de expiración predeterminado es de un minuto. El tiempo de expiración protege al código de una pérdida en la conectividad de red o de código JavaScript que nunca devuelve un mensaje de finalización. Si se agota el tiempo de espera de la llamada, se produce un error en el objeto <xref:System.Threading.Tasks> resultante con una excepción <xref:System.OperationCanceledException>. Capture y procese la excepción con el registro.

Del mismo modo, el código de JavaScript puede iniciar llamadas a métodos de .NET indicados por el atributo [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript). Si estos métodos de .NET inician una excepción no controlada:

* La excepción no se trata como grave para un circuito de Blazor Server.
* Se rechaza el objeto `Promise` del lado de JavaScript.

Tiene la opción de usar el código de control de errores en el lado de .NET o en el de JavaScript de la llamada de método.

Para obtener más información, vea los artículos siguientes:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Representación previa de Blazor Server

Se puede realizar la representación previa de los componentes de Blazor mediante el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para que su marcado HTML representado se devuelva como parte de la solicitud HTTP inicial del usuario. El funcionamiento es el siguiente:

* Se crea un circuito para todos los componentes con representación previa que forman parte de la misma página.
* Se genera el código HTML inicial.
* El circuito se trata como `disconnected` hasta que el explorador del usuario establece una conexión de SignalR al mismo servidor. Cuando se establece la conexión, se reanuda la interactividad en el circuito y se actualiza el marcado HTML de los componentes.

Si algún componente inicia una excepción no controlada durante la representación previa, por ejemplo, durante un método de ciclo de vida o en la lógica de representación:

* La excepción es grave para el circuito.
* La excepción se inicia en la pila de llamadas desde el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>. Por tanto, se produce un error en toda la solicitud HTTP a menos que el código del desarrollador detecte la excepción de forma explícita.

En circunstancias normales, cuando se produce un error en la representación previa, continuar con la generación y representación del componente no tiene sentido, ya que un componente en funcionamiento no se puede representar.

Para tolerar los errores que se puedan producir durante la representación previa, se debe colocar la lógica de control de errores dentro de un componente que pueda iniciar excepciones. Use instrucciones [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) con control de errores y registro. En lugar de encapsular el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> en una instrucción [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch), coloque la lógica de control de errores en el componente representado por el asistente de etiquetas <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>.

## <a name="advanced-scenarios"></a>Escenarios avanzados

### <a name="recursive-rendering"></a>Representación recursiva

Los componentes se pueden anidar de forma recursiva. Esto resulta útil para representar estructuras de datos recursivas. Por ejemplo, un componente `TreeNode` puede representar más componentes `TreeNode` para cada uno de los elementos secundarios del nodo.

Al realizar la representación de forma recursiva, evite patrones de codificación que produzcan una recursión infinita:

* No represente de forma recursiva una estructura de datos que contenga un ciclo. Por ejemplo, no represente un nodo de árbol cuyos elementos secundarios se incluyan a sí mismos.
* No cree una cadena de diseños que contengan un ciclo. Por ejemplo, no debe crear un diseño cuyo diseño sea él mismo.
* No permita que un usuario final incumpla las invariables de recursión (reglas) a través de entradas de datos malintencionadas o llamadas de interoperabilidad de JavaScript.

Bucles infinitos durante la representación:

* Hace que el proceso de representación continúe de manera indefinida.
* Equivale a crear un bucle sin terminar.

En estos escenarios, se produce un error en un circuito de Blazor Server afectado y el subproceso normalmente intenta lo siguiente:

* Consumir el máximo tiempo de CPU que permita el sistema operativo, de manera indefinida.
* Consumir una cantidad ilimitada de memoria del servidor. El consumo de memoria ilimitada es equivalente al escenario en el que un bucle sin terminar agrega entradas a una colección en cada iteración.

Para evitar patrones de recursión, asegúrese de que el código de representación recursiva contenga condiciones de detención adecuadas.

### <a name="custom-render-tree-logic"></a>Lógica de árbol de representación personalizada

La mayoría de los componentes Blazor se implementan como archivos `.razor` y se compilan para generar lógica que opere en un objeto <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> y representar su salida. Un desarrollador puede implementar de forma manual lógica de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> mediante código de C# por procedimientos. Para obtener más información, vea <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> El uso de la lógica del generador de árboles de representación manual se considera un escenario avanzado y no seguro, no recomendado para el desarrollo de componentes generales.

Si se escribe código de <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, el desarrollador debe garantizar la corrección del código. Por ejemplo, el desarrollador debe asegurarse de que:

* Las llamadas a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> y <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> se equilibran correctamente.
* Los atributos solo se agregan en los lugares correctos.

Una lógica incorrecta del generador de árboles de representación manual puede producir un comportamiento indefinido arbitrario, incluidos bloqueos, bloqueos del servidor y vulnerabilidades de seguridad.

Considere la posibilidad de usar lógica de generador de árbol de representación manual en el mismo nivel de complejidad y con el mismo nivel de *peligro* como escribir código de ensamblado o instrucciones MSIL a mano.
