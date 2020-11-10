---
title: Novedades de ASP.NET Core 5.0
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Kestrel):::'
uid: aspnetcore-5.0
ms.openlocfilehash: e9c74f7b45ebcdffc19a0483b4e98ad2f44d5747
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061754"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="8c5cb-103">Novedades de ASP.NET Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8c5cb-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="8c5cb-104">En este artículo se resaltan los cambios más importantes de ASP.NET Core 5.0, con vínculos a la documentación pertinente.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="8c5cb-105">Mejoras en ASP.NET Core MVC y :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="8c5cb-105">ASP.NET Core MVC and :::no-loc(Razor)::: improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="8c5cb-106">Enlace de modelos DateTime como UTC</span><span class="sxs-lookup"><span data-stu-id="8c5cb-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="8c5cb-107">El enlace de modelos admite ahora las cadenas de hora UTC de enlace para `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="8c5cb-108">Si la solicitud contiene una cadena de hora UTC, el enlace de modelos la enlaza a una `DateTime` UTC.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="8c5cb-109">Por ejemplo, la siguiente cadena de tiempo está enlazada a la hora UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="8c5cb-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="8c5cb-110">Enlace de modelos y validación con los nueve tipos de registro de C#</span><span class="sxs-lookup"><span data-stu-id="8c5cb-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="8c5cb-111">Los [nueve tipos de registros de C#](/dotnet/csharp/whats-new/csharp-9#record-types) pueden usarse con el enlace de modelos en un controlador MVC o una página :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="8c5cb-112">Los tipos de registros son una buena manera de modelar los datos que se transmiten a través de la red.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="8c5cb-113">Por ejemplo, el `PersonController` siguiente usa el tipo de registro `Person` con el enlace de modelos y la validación de formularios:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="8c5cb-114">El archivo `Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="8c5cb-115">Mejoras en DynamicRouteValueTransformer</span><span class="sxs-lookup"><span data-stu-id="8c5cb-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="8c5cb-116">ASP.NET Core 3.1 presentó <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> como una manera de usar un punto de conexión personalizado para seleccionar dinámicamente una acción del controlador MVC o una página :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a :::no-loc(Razor)::: page.</span></span> <span data-ttu-id="8c5cb-117">ASP.NET Core 5.0 aplicaciones pueden pasar el estado a un `DynamicRouteValueTransformer` y filtrar el conjunto de puntos de conexión elegidos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="8c5cb-118">Varios</span><span class="sxs-lookup"><span data-stu-id="8c5cb-118">Miscellaneous</span></span>

* <span data-ttu-id="8c5cb-119">El atributo [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) se puede aplicar a las propiedades en un modelo de página de :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a :::no-loc(Razor)::: Page model.</span></span>
* <span data-ttu-id="8c5cb-120">Los parámetros y las propiedades enlazadas desde el cuerpo se consideran necesarios de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="8c5cb-121">Web API</span><span class="sxs-lookup"><span data-stu-id="8c5cb-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="8c5cb-122">Especificación OpenAPI activada de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="8c5cb-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="8c5cb-123">La [especificación OpenAPI](http://spec.openapis.org/oas/v3.0.3) es un estándar del sector para describir las API HTTP e integrarlas en procesos empresariales complejos o con terceros.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="8c5cb-124">OpenAPI es ampliamente compatible con todos los proveedores de nube y muchos registros de API.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="8c5cb-125">Las aplicaciones que emiten documentos de OpenAPI desde API web tienen una variedad de nuevas oportunidades en las que se pueden usar esas API.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="8c5cb-126">En colaboración con los mantenedores del proyecto de código abierto [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), la plantilla de API de ASP.NET Core contiene una dependencia de NuGet en [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="8c5cb-127">Swashbuckle es un conocido paquete de NuGet de código abierto que envía documentos OpenAPI de forma dinámica.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="8c5cb-128">Swashbuckle lo hace examinando los controladores de la API y generando el documento OpenAPI en tiempo de ejecución, o en tiempo de compilación usando la CLI de Swashbuckle.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="8c5cb-129">En ASP.NET Core 5.0, las plantillas de API web habilitan la compatibilidad con OpenAPI de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="8c5cb-130">Para deshabilitar OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="8c5cb-131">Desde la línea de comandos:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="8c5cb-132">Desde Visual Studio: Desactive **Habilitar la compatibilidad de OpenAPI**.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="8c5cb-133">Todos los archivos *.csproj* creados para los proyectos de API web contienen la referencia del paquete NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="8c5cb-134">El código generado por la plantilla contiene código en `Startup.ConfigureServices` que activa la generación de documentos OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="8c5cb-135">El método `Startup.Configure` agrega el middleware Swashbuckle, que permite:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="8c5cb-136">El proceso de generación de documentos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-136">Document generation process.</span></span>
* <span data-ttu-id="8c5cb-137">La página de interfaz de usuario de Swagger de forma predeterminada en modo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="8c5cb-138">El código generado por la plantilla no expone accidentalmente la descripción de la API al publicar en producción.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="8c5cb-139">Importación de Azure API Management</span><span class="sxs-lookup"><span data-stu-id="8c5cb-139">Azure API Management Import</span></span>

<span data-ttu-id="8c5cb-140">Cuando los proyectos API de ASP.NET Core habilitan OpenAPI, la publicación de la versión 16.8 y posteriores de Visual Studio 2019 proporciona automáticamente un paso adicional en el flujo de publicación.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="8c5cb-141">Los desarrolladores que usan [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) tienen la oportunidad de importar automáticamente las API en Azure API Management durante el flujo de publicación:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Importación y publicación de Azure API Management](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="8c5cb-143">Mejor experiencia de inicio para los proyectos de API web</span><span class="sxs-lookup"><span data-stu-id="8c5cb-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="8c5cb-144">Con OpenAPI habilitado de forma predeterminada, la experiencia de inicio de la aplicación (F5) para desarrolladores de API web mejora significativamente.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="8c5cb-145">Con ASP.NET Core 5.0, la plantilla de API web viene preconfigurada para cargar la página de la interfaz de usuario de Swagger.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="8c5cb-146">La página de la interfaz de usuario de Swagger proporciona la documentación agregada a la API publicada y permite probar las API con un solo clic.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![Vista swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## :::no-loc(Blazor):::

### <a name="performance-improvements"></a><span data-ttu-id="8c5cb-148">Mejoras en el rendimiento</span><span class="sxs-lookup"><span data-stu-id="8c5cb-148">Performance improvements</span></span>

<span data-ttu-id="8c5cb-149">En el caso de .NET 5, hemos realizado mejoras significativas en el rendimiento del entorno de ejecución :::no-loc(Blazor WebAssembly)::: con un foco específico en la representación de la interfaz de usuario y la serialización de JSON complejos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-149">For .NET 5, we made significant improvements to :::no-loc(Blazor WebAssembly)::: runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="8c5cb-150">En nuestras pruebas de rendimiento, :::no-loc(Blazor WebAssembly)::: en .NET 5 es dos a tres veces más rápido en la mayoría de los escenarios.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-150">In our performance tests, :::no-loc(Blazor WebAssembly)::: in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="8c5cb-151">Para más información, consulte el [blog de ASP.NET: Actualizaciones de ASP.NET Core en .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="8c5cb-152">Aislamiento de CSS</span><span class="sxs-lookup"><span data-stu-id="8c5cb-152">CSS isolation</span></span>

<span data-ttu-id="8c5cb-153">:::no-loc(Blazor)::: ahora admite la definición de estilos CSS cuyo ámbito es un componente determinado.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-153">:::no-loc(Blazor)::: now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="8c5cb-154">Los estilos CSS específicos de los componentes facilitan el razonamiento de los estilos de una aplicación y evitan efectos secundarios no intencionados de los estilos globales.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="8c5cb-155">Para obtener más información, vea <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="8c5cb-156">Nuevo componente de `InputFile`</span><span class="sxs-lookup"><span data-stu-id="8c5cb-156">New `InputFile` component</span></span>

<span data-ttu-id="8c5cb-157">El componente `InputFile` permite leer uno o varios archivos seleccionados por un usuario para cargarlos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="8c5cb-158">Para obtener más información, vea <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="8c5cb-159">Nuevos componentes `InputRadio` y `InputRadioGroup`</span><span class="sxs-lookup"><span data-stu-id="8c5cb-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="8c5cb-160">:::no-loc(Blazor)::: tiene los componentes integrados `InputRadio` y `InputRadioGroup` que simplifican el enlace de datos a grupos de botones de radio con validación integrada.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-160">:::no-loc(Blazor)::: has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="8c5cb-161">Para obtener más información, vea <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="8c5cb-162">Virtualización de componentes</span><span class="sxs-lookup"><span data-stu-id="8c5cb-162">Component virtualization</span></span>

<span data-ttu-id="8c5cb-163">Mejore el rendimiento percibido de la representación de componentes usando la compatibilidad de virtualización integrada del marco :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-163">Improve the perceived performance of component rendering using the :::no-loc(Blazor)::: framework's built-in virtualization support.</span></span> <span data-ttu-id="8c5cb-164">Para obtener más información, vea <xref:blazor/forms-validation#radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="8c5cb-165">Compatibilidad con eventos `ontoggle`</span><span class="sxs-lookup"><span data-stu-id="8c5cb-165">`ontoggle` event support</span></span>

<span data-ttu-id="8c5cb-166">Los eventos :::no-loc(Blazor)::: ahora admiten el evento DOM `ontoggle`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-166">:::no-loc(Blazor)::: events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="8c5cb-167">Para obtener más información, vea <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="8c5cb-168">Establecimiento del foco de la interfaz de usuario en aplicaciones :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="8c5cb-168">Set UI focus in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="8c5cb-169">Use el método de conveniencia `FocusAsync` en las referencias de elemento para establecer el foco de la interfaz de usuario en ese elemento.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="8c5cb-170">Para obtener más información, vea <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="8c5cb-171">Atributos de clase de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="8c5cb-171">Custom validation class attributes</span></span>

<span data-ttu-id="8c5cb-172">Los nombres de clase de validación personalizados son útiles al integrar con marcos de CSS, como Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="8c5cb-173">Para obtener más información, vea <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="8c5cb-174">Compatibilidad con IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="8c5cb-174">IAsyncDisposable support</span></span>

<span data-ttu-id="8c5cb-175">Los componentes de :::no-loc(Blazor)::: ahora admiten la interfaz de <xref:System.IAsyncDisposable> para la versión asincrónica de los recursos asignados.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-175">:::no-loc(Blazor)::: components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="8c5cb-176">Aislamiento de JavaScript y referencias a objetos en </span><span class="sxs-lookup"><span data-stu-id="8c5cb-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="8c5cb-177">:::no-loc(Blazor)::: permite el aislamiento de JavaScript en [módulos de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) estándar.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-177">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="8c5cb-178">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="8c5cb-179">Nombre para mostrar de la compatibilidad de componentes de formulario</span><span class="sxs-lookup"><span data-stu-id="8c5cb-179">Form components support display name</span></span>

<span data-ttu-id="8c5cb-180">Los siguientes componentes integrados admiten nombres para mostrar con el parámetro `DisplayName`:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="8c5cb-181">Para obtener más información, vea <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="8c5cb-182">Parámetros de ruta de captura general</span><span class="sxs-lookup"><span data-stu-id="8c5cb-182">Catch-all route parameters</span></span>

<span data-ttu-id="8c5cb-183">Los parámetros de ruta de captura general, que capturan rutas de acceso en varios límites de carpeta, se admiten en los componentes.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="8c5cb-184">Para obtener más información, vea <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="8c5cb-185">Mejoras en la depuración</span><span class="sxs-lookup"><span data-stu-id="8c5cb-185">Debugging improvements</span></span>

<span data-ttu-id="8c5cb-186">La depuración de aplicaciones :::no-loc(Blazor WebAssembly)::: ha mejorado en ASP.NET Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-186">Debugging :::no-loc(Blazor WebAssembly)::: apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="8c5cb-187">Además, ahora se admite la depuración en Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="8c5cb-188">Para obtener más información, vea <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="8c5cb-189">Microsoft :::no-loc(Identity)::: v2.0 y MSAL v2.0</span><span class="sxs-lookup"><span data-stu-id="8c5cb-189">Microsoft :::no-loc(Identity)::: v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="8c5cb-190">La seguridad de :::no-loc(Blazor)::: ahora usa Microsoft :::no-loc(Identity)::: v2.0 ([`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) y [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)) y MSAL v2.0.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-190">:::no-loc(Blazor)::: security now uses Microsoft :::no-loc(Identity)::: v2.0 ([`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) and [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="8c5cb-191">Para más información, consulte los temas de la seguridad de [:::no-loc(Blazor)::: y del nodo :::no-loc(Identity):::](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-191">For more information, see the topics in the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="8c5cb-192">Almacenamiento de explorador protegido para :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="8c5cb-192">Protected Browser Storage for :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="8c5cb-193">Las aplicaciones de :::no-loc(Blazor Server)::: ahora pueden usar la compatibilidad integrada para almacenar el estado de la aplicación en el explorador que se ha protegido contra la manipulación mediante la protección de datos de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-193">:::no-loc(Blazor Server)::: apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="8c5cb-194">Los datos se pueden almacenar en el almacenamiento de explorador local o en el almacenamiento de sesión.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="8c5cb-195">Para obtener más información, vea <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="8c5cb-196">Representación previa de :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="8c5cb-196">:::no-loc(Blazor WebAssembly)::: prerendering</span></span>

<span data-ttu-id="8c5cb-197">La integración de componentes se ha mejorado en los modelos de hospedaje y las aplicaciones de :::no-loc(Blazor WebAssembly)::: ahora pueden representar la salida en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-197">Component integration is improved across hosting models, and :::no-loc(Blazor WebAssembly)::: apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="8c5cb-198">Mejoras en el recorte o la vinculación</span><span class="sxs-lookup"><span data-stu-id="8c5cb-198">Trimming/linking improvements</span></span>

<span data-ttu-id="8c5cb-199">:::no-loc(Blazor WebAssembly)::: realiza la vinculación o recorte del lenguaje intermedio (IL) durante una compilación para recortar el lenguaje intermedio innecesario de los ensamblados de salida de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-199">:::no-loc(Blazor WebAssembly)::: performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="8c5cb-200">Con la versión de ASP.NET Core 5.0, :::no-loc(Blazor WebAssembly)::: realiza un recorte mejorado con opciones de configuración adicionales.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-200">With the release of ASP.NET Core 5.0, :::no-loc(Blazor WebAssembly)::: performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="8c5cb-201">Para obtener más información, consulte <xref:blazor/host-and-deploy/configure-trimmer> y [Opciones de recorte](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="8c5cb-202">Analizador de compatibilidad con el explorador</span><span class="sxs-lookup"><span data-stu-id="8c5cb-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="8c5cb-203">Las aplicaciones :::no-loc(Blazor WebAssembly)::: tienen como destino el área expuesta de la API de .NET completa, pero no todas las API de .NET son compatibles con WebAssembly, debido a las restricciones de espacio aislado del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-203">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="8c5cb-204">Las API no compatibles inician la excepción <xref:System.PlatformNotSupportedException> cuando se ejecutan en WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="8c5cb-205">Un analizador de compatibilidad de plataforma advierte al desarrollador cuando la aplicación usa API que no son compatibles con las plataformas de destino de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="8c5cb-206">Para obtener más información, vea <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="8c5cb-207">Ensamblados de carga diferida</span><span class="sxs-lookup"><span data-stu-id="8c5cb-207">Lazy load assemblies</span></span>

<span data-ttu-id="8c5cb-208">Se puede mejorar el rendimiento de inicio de la aplicación :::no-loc(Blazor WebAssembly)::: aplazando la carga de algunos ensamblados de la aplicación hasta que sean necesarios.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-208">:::no-loc(Blazor WebAssembly)::: app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="8c5cb-209">Para obtener más información, vea <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="8c5cb-210">Compatibilidad de globalización actualizada</span><span class="sxs-lookup"><span data-stu-id="8c5cb-210">Updated globalization support</span></span>

<span data-ttu-id="8c5cb-211">La compatibilidad con la globalización está disponible para :::no-loc(Blazor WebAssembly)::: basada en componentes internacionales para Unicode (ICU).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-211">Globalization support is available for :::no-loc(Blazor WebAssembly)::: based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="8c5cb-212">Para obtener más información, vea <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="8c5cb-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="8c5cb-213">gRPC</span></span>

<span data-ttu-id="8c5cb-214">Se han realizado muchas mejoras de conformidad en [gRPC](https://grpc.io/).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="8c5cb-215">Para más información, consulte [Mejoras de rendimiento de gRPC en .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="8c5cb-216">Para más información sobre gRPC, consulte <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## :::no-loc(SignalR):::

<span data-ttu-id="8c5cb-217">Los filtros del concentrador :::no-loc(SignalR):::, denominados canalizaciones de concentrador en ASP.NET :::no-loc(SignalR):::, es una característica que permite que el código se ejecute antes y después de que se llame a los métodos de concentrador.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-217">:::no-loc(SignalR)::: Hub filters, called Hub pipelines in ASP.NET :::no-loc(SignalR):::, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="8c5cb-218">Ejecutar el código antes y después de llamar a los métodos de concentrador es similar a cómo el middleware tiene la capacidad de ejecutar el código antes y después de una solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-218">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="8c5cb-219">Entre los usos habituales se incluyen el registro, el control de errores y la validación de argumentos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-219">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="8c5cb-220">Para más información, consulte [Uso de filtros del concentrador en ASP.NET:::no-loc(SignalR):::](xref:signalr/hub-filters).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-220">For more information, see [Use hub filters in ASP.NET Core :::no-loc(SignalR):::](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="8c5cb-221">Invocaciones de concentrador paralelo de :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="8c5cb-221">:::no-loc(SignalR)::: parallel hub invocations</span></span>

<span data-ttu-id="8c5cb-222">:::no-loc(SignalR)::: de ASP.NET Core ahora es capaz de controlar las invocaciones del concentrador en paralelo.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-222">ASP.NET Core :::no-loc(SignalR)::: is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="8c5cb-223">Se puede cambiar el comportamiento predeterminado para permitir que los clientes invoquen más de un método de concentrador a la vez:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-223">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/Startup:::no-loc(SignalR):::hubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="8c5cb-224">Se ha agregado compatibilidad con Messagepack en el cliente de Java :::no-loc(SignalR):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-224">Added Messagepack support in :::no-loc(SignalR)::: Java client</span></span>

<span data-ttu-id="8c5cb-225">Un nuevo paquete, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), agrega compatibilidad con MessagePack al cliente de Java :::no-loc(SignalR):::.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-225">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the :::no-loc(SignalR)::: Java client.</span></span> <span data-ttu-id="8c5cb-226">Para usar el protocolo del concentrador de MessagePack, agregue `.withHubProtocol(new MessagePackHubProtocol())` al generador de conexiones:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-226">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update :::no-loc(SignalR)::: code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## :::no-loc(Kestrel):::

* <span data-ttu-id="8c5cb-227">Los puntos de conexión recargables mediante la configuración :::no-loc(Kestrel)::: puede detectar cambios en la configuración pasada a [:::no-loc(Kestrel):::ServerOptions.Configure](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) y desenlace de los puntos de conexión existentes y enlace a nuevos puntos de conexión sin necesidad de reiniciar la aplicación cuando el parámetro `reloadOnChange` es `true`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-227">Reloadable endpoints via configuration: :::no-loc(Kestrel)::: can detect changes to configuration passed to [:::no-loc(Kestrel):::ServerOptions.Configure](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="8c5cb-228">De forma predeterminada, cuando se usa <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, :::no-loc(Kestrel)::: se enlaza a la subsección de la configuración [":::no-loc(Kestrel):::"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) con `reloadOnChange` habilitado.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-228">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, :::no-loc(Kestrel)::: binds to the [":::no-loc(Kestrel):::"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="8c5cb-229">Las aplicaciones deben pasar `reloadOnChange: true` al llamar a `:::no-loc(Kestrel):::ServerOptions.Configure` manualmente para obtener los puntos de conexión que se van a cargar.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-229">Apps must pass `reloadOnChange: true` when calling `:::no-loc(Kestrel):::ServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="8c5cb-230">Mejoras de los encabezados de respuesta HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8c5cb-230">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="8c5cb-231">Para más información, consulte [Mejoras de rendimiento](#performance-improvements) en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-231">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="8c5cb-232">Compatibilidad con tipos de puntos de conexión adicionales en el transporte de sockets: Al agregar a la nueva API introducida en <xref:System.Net.Sockets?displayProperty=nameWithType>, el transporte predeterminado de sockets en [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) permite el enlace tanto a los identificadores de archivo existentes como a los sockets de dominio de Unix.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-232">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="8c5cb-233">La compatibilidad con el enlace a los identificadores de archivos existentes permite el uso de la integración de `Systemd` existente sin necesidad del transporte de `libuv`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-233">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="8c5cb-234">Descodificación de encabezado personalizada en :::no-loc(Kestrel):::: Las aplicaciones pueden especificar qué propiedad <xref:System.Text.Encoding> se va a usar para interpretar los encabezados entrantes basándose en el nombre del encabezado en lugar de establecerse en UTF-8 de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-234">Custom header decoding in :::no-loc(Kestrel):::: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="8c5cb-235">Pase el conmutador </span><span class="sxs-lookup"><span data-stu-id="8c5cb-235">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="8c5cb-236">Propiedad `Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` para especificar la codificación que se va a usar:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-236">`Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="8c5cb-237">Opciones específicas del punto de conexión de :::no-loc(Kestrel)::: mediante configuración</span><span class="sxs-lookup"><span data-stu-id="8c5cb-237">:::no-loc(Kestrel)::: endpoint-specific options via configuration</span></span>

<span data-ttu-id="8c5cb-238">Se ha agregado compatibilidad con la configuración de las opciones específicas del punto de conexión de :::no-loc(Kestrel)::: mediante [configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-238">Support has been added for configuring :::no-loc(Kestrel):::’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="8c5cb-239">Las configuraciones específicas del punto de conexión incluyen:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-239">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="8c5cb-240">Protocolos HTTP usados</span><span class="sxs-lookup"><span data-stu-id="8c5cb-240">HTTP protocols used</span></span>
* <span data-ttu-id="8c5cb-241">Protocolos TLS</span><span class="sxs-lookup"><span data-stu-id="8c5cb-241">TLS protocols used</span></span>
* <span data-ttu-id="8c5cb-242">Certificado seleccionado</span><span class="sxs-lookup"><span data-stu-id="8c5cb-242">Certificate selected</span></span>
* <span data-ttu-id="8c5cb-243">Modo de certificado de cliente</span><span class="sxs-lookup"><span data-stu-id="8c5cb-243">Cient certificate mode</span></span>

<span data-ttu-id="8c5cb-244">La configuración permite especificar qué certificado se selecciona en función del nombre de servidor especificado.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-244">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="8c5cb-245">El nombre del servidor forma parte de la extensión de Indicación de nombre de servidor (SNI) al protocolo TLS, como indica el cliente.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-245">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="8c5cb-246">La configuración de :::no-loc(Kestrel)::: también admite un prefijo de carácter comodín en el nombre de host.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-246">:::no-loc(Kestrel):::'s configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="8c5cb-247">En el ejemplo siguiente se muestra cómo especificar un punto de conexión específico mediante un archivo de configuración:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-247">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  ":::no-loc(Kestrel):::": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="8c5cb-248">Indicación de nombre de servidor (SNI) es una extensión de TLS para incluir un dominio virtual como parte de la negociación SSL.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-248">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="8c5cb-249">Lo que esto significa efectivamente es que el nombre de dominio virtual, o un nombre de host, puede utilizarse para identificar el punto de conexión de la red.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-249">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="8c5cb-250">Mejoras en el rendimiento</span><span class="sxs-lookup"><span data-stu-id="8c5cb-250">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="8c5cb-251">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8c5cb-251">HTTP/2</span></span>

* <span data-ttu-id="8c5cb-252">Reducciones significativas en las asignaciones en la ruta de acceso del código HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-252">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="8c5cb-253">Compatibilidad con la [compresión dinámica HPack](https://tools.ietf.org/html/rfc7541) de encabezados de respuesta HTTP/2 en [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-253">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="8c5cb-254">Para más información, consulte [Tamaño de tabla de encabezado](xref:fundamentals/servers/kestrel#header-table-size) y [HPACK: la característica silenciosa de HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-254">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="8c5cb-255">Envío de tramas PING HTTP/2: HTTP/2 tiene un mecanismo para enviar tramas de PING para asegurarse de que sigue siendo funcional una conexión inactiva.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-255">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="8c5cb-256">Asegurarse de que una conexión viable es especialmente útil cuando se trabaja con flujos de larga duración que suelen estar inactivos, pero solo se ven de forma intermitente las actividades, por ejemplo, las secuencias de gRPC.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-256">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="8c5cb-257">Las aplicaciones pueden enviar tramas de PING periódicas en [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) estableciendo límites en <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions>:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-257">Apps can send periodic PING frames in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="8c5cb-258">Contenedores</span><span class="sxs-lookup"><span data-stu-id="8c5cb-258">Containers</span></span>

<span data-ttu-id="8c5cb-259">Antes de .NET 5.0, la creación y publicación de un *Dockerfile* para una aplicación ASP.NET Core necesitaba extraer toda la SDK de .NET Core y la imagen de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-259">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="8c5cb-260">Con esta versión, se reduce el número de bytes de las imágenes del SDK y los bytes extraídos para la imagen de ASP.NET Core se eliminan en gran medida.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-260">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="8c5cb-261">Para más información, consulte [este comentario de un problema de GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-261">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="8c5cb-262">Autenticación y autorización</span><span class="sxs-lookup"><span data-stu-id="8c5cb-262">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="8c5cb-263">Autenticación de Azure Active Directory con Microsoft.:::no-loc(Identity):::.Web</span><span class="sxs-lookup"><span data-stu-id="8c5cb-263">Azure Active Directory authentication with Microsoft.:::no-loc(Identity):::.Web</span></span>

<span data-ttu-id="8c5cb-264">Las plantillas de proyecto de ASP.NET Core ahora se integran con <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> para controlar la autenticación con [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-264">The ASP.NET Core project templates now integrate with <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="8c5cb-265">El [paquete Microsoft.:::no-loc(Identity):::.Web](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) proporciona:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-265">The [Microsoft.:::no-loc(Identity):::.Web package](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) provides:</span></span>

* <span data-ttu-id="8c5cb-266">Una mejor experiencia para la autenticación a través de Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-266">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="8c5cb-267">Una manera más sencilla de acceder a los recursos de Azure en nombre de los usuarios, incluido [Microsoft Graph](/graph/overview).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-267">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="8c5cb-268">Consulte el [ejemplo Microsoft.:::no-loc(Identity):::.Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), que comienza con un inicio de sesión básico y avanza a través de varios inquilinos, mediante el uso de las API de Azure, el uso de Microsoft Graph y la protección de sus propias API.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-268">See the [Microsoft.:::no-loc(Identity):::.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="8c5cb-269">`Microsoft.:::no-loc(Identity):::.Web` está disponible junto con .NET 5.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-269">`Microsoft.:::no-loc(Identity):::.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="8c5cb-270">Acceso anónimo permitido a un punto de conexión</span><span class="sxs-lookup"><span data-stu-id="8c5cb-270">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="8c5cb-271">El método de extensión `AllowAnonymous` permite el acceso anónimo a un punto de conexión:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-271">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="8c5cb-272">Control personalizado de errores de autorización</span><span class="sxs-lookup"><span data-stu-id="8c5cb-272">Custom handling of authorization failures</span></span>

<span data-ttu-id="8c5cb-273">El control personalizado de los errores de autorización es ahora más fácil con la nueva interfaz [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) que invoca la [autorización](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-273">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="8c5cb-274">La implementación predeterminada sigue siendo la misma, pero se puede registrar un controlador personalizado en [inserción de dependencias, que permite respuestas HTTP personalizadas basadas en el motivo del error de la autorización.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-274">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="8c5cb-275">Consulte [este ejemplo](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) que muestra el uso de la `IAuthorizationMiddlewareResultHandler`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-275">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="8c5cb-276">Autorización al usar el enrutamiento de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="8c5cb-276">Authorization when using endpoint routing</span></span>

<span data-ttu-id="8c5cb-277">La autorización al usar el enrutamiento del punto de conexión ahora recibe `HttpContext` en lugar de la instancia del punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-277">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="8c5cb-278">Esto permite que el middleware de autorización tenga acceso a `RouteData` y otras propiedades de `HttpContext` a las que no se pudo tener acceso a través de la clase <xref:Microsoft.AspNetCore.Http.Endpoint>.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-278">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="8c5cb-279">El punto de conexión se puede capturar desde el contexto mediante [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-279">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="8c5cb-280">Control de acceso basado en roles con autenticación Kerberos y LDAP en Linux</span><span class="sxs-lookup"><span data-stu-id="8c5cb-280">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="8c5cb-281">Consulte [Autenticación Kerberos y control de acceso basado en rol (RBAC)](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="8c5cb-281">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="8c5cb-282">Mejoras en la API</span><span class="sxs-lookup"><span data-stu-id="8c5cb-282">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="8c5cb-283">Métodos de extensión JSON para HttpRequest y HttpResponse</span><span class="sxs-lookup"><span data-stu-id="8c5cb-283">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="8c5cb-284">Los datos JSON se pueden leer y escribir desde un `HttpRequest` y `HttpResponse` mediante los nuevos métodos de extensión <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> y `WriteAsJsonAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-284">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="8c5cb-285">Estos métodos de extensión utilizan el serializador [System.Text.JSON](xref:System.Text.Json) para controlar los datos JSON.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-285">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="8c5cb-286">El nuevo método de extensión de `HasJsonContentType` también puede comprobar si una solicitud tiene un tipo de contenido JSON.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-286">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="8c5cb-287">Los métodos de extensión JSON se pueden combinar con el [enrutamiento de punto de conexión](xref:fundamentals/routing) para crear las API JSON en un estilo de programación que llamamos \* **ruta al código** _.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-287">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \* **route to code** _.</span></span> <span data-ttu-id="8c5cb-288">Es una opción nueva para los desarrolladores que desean crear las API JSON básicas de una manera ligera.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-288">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="8c5cb-289">Por ejemplo, una aplicación web que tiene solo unos cuantos puntos de conexión puede optar por usar la ruta al código en lugar de la funcionalidad completa de ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-289">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="8c5cb-290">Para más información sobre los nuevos métodos de extensión JSON y cómo enrutar al código, consulte [este programa de .NET](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="8c5cb-290">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="8c5cb-291">System.Diagnostics.Activity</span><span class="sxs-lookup"><span data-stu-id="8c5cb-291">System.Diagnostics.Activity</span></span>

<span data-ttu-id="8c5cb-292">El formato predeterminado de <xref:System.Diagnostics.Activity?displayProperty=fullName> ahora tiene como valor predeterminado el formato W3C.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-292">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="8c5cb-293">Esto hace que la compatibilidad con la traza distribuida en ASP.NET Core interoperable con más marcos de trabajo de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-293">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="8c5cb-294">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="8c5cb-294">FromBodyAttribute</span></span>

<span data-ttu-id="8c5cb-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> anora admite la configuración de una opción que permite que estos parámetros o propiedades se consideren opcionales:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ow supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="8c5cb-296">Otras mejoras</span><span class="sxs-lookup"><span data-stu-id="8c5cb-296">Miscellaneous improvements</span></span>

<span data-ttu-id="8c5cb-297">Hemos empezado a aplicar las [anotaciones que aceptan valores NULL](/dotnet/csharp/nullable-references#attributes-describe-apis) a ensamblados ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-297">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="8c5cb-298">Tenemos previsto anotar la mayor parte de la superficie común de la API pública de .NET 5 Framework.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-298">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="8c5cb-299">Control de la activación de la clase de inicio</span><span class="sxs-lookup"><span data-stu-id="8c5cb-299">Control Startup class activation</span></span>

<span data-ttu-id="8c5cb-300">Se ha agregado una sobrecarga de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> adicional que permite que una aplicación proporcione un Factory Method para controlar la activación de la clase `Startup`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-300">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="8c5cb-301">El control de la activación de la clase `Startup` es útil para pasar parámetros adicionales a `Startup` que se inicializan junto con el host:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-301">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="8c5cb-302">Actualización automática con dotnet watch</span><span class="sxs-lookup"><span data-stu-id="8c5cb-302">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="8c5cb-303">En .NET 5, la ejecución de [dotnet watch](xref:tutorials/dotnet-watch) en un proyecto de ASP.NET Core inicia el explorador predeterminado y actualiza automáticamente el explorador a medida que se realizan cambios en el código.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-303">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="8c5cb-304">Esto significa que puede:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-304">This means you can:</span></span>

<span data-ttu-id="8c5cb-305">_ Abrir un proyecto de ASP.NET Core en un editor de texto.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-305">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="8c5cb-306">Ejecute `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-306">Run `dotnet watch`.</span></span>
* <span data-ttu-id="8c5cb-307">Céntrese en los cambios de código mientras las herramientas controlan la reconstrucción, el reinicio y la recarga de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-307">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="8c5cb-308">Esperamos incorporar la funcionalidad de actualización automática a Visual Studio en el futuro.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-308">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="8c5cb-309">Formateador del registrador de consola</span><span class="sxs-lookup"><span data-stu-id="8c5cb-309">Console Logger Formatter</span></span>

<span data-ttu-id="8c5cb-310">Se han realizado mejoras en el proveedor de registro de la consola en la biblioteca `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="8c5cb-311">Los desarrolladores ahora pueden implementar un `ConsoleFormatter` personalizado para ejercer el control completo sobre el formato y el color de la salida de la consola.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="8c5cb-312">Las API de formateador permiten un formato enriquecido mediante la implementación de un subconjunto de las secuencias de escape VT-100.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="8c5cb-313">VT-100 es compatible con la mayoría de los terminales modernos.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="8c5cb-314">El registrador de consola puede analizar las secuencias de escape en los terminales no compatibles, lo que permite a los desarrolladores crear un solo formateador para todos los terminales.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="8c5cb-315">Registrador de la consola JSON</span><span class="sxs-lookup"><span data-stu-id="8c5cb-315">JSON Console Logger</span></span>

<span data-ttu-id="8c5cb-316">Además de admitir los formateadores personalizados, también hemos agregado un formateador JSON integrado que emite registros de JSON estructurados en la consola.</span><span class="sxs-lookup"><span data-stu-id="8c5cb-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="8c5cb-317">En el código siguiente se muestra cómo cambiar del registrador predeterminado a JSON:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="8c5cb-318">Los mensajes de registro que se emiten a la consola tienen formato JSON:</span><span class="sxs-lookup"><span data-stu-id="8c5cb-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
