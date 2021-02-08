---
title: Creación de API web con ASP.NET Core
author: scottaddie
description: Conozca los aspectos básicos de la creación de una API web en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: 03debcb211a8cb8f0ebd6d5e67fbc8de7b574e27
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551655"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="1b510-103">Creación de API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b510-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="1b510-104">Por [Scott Addie](https://github.com/scottaddie) y [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1b510-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1b510-105">ASP.NET Core admite la creación de servicios RESTful, lo que también se conoce como API web, mediante C#.</span><span class="sxs-lookup"><span data-stu-id="1b510-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="1b510-106">Para gestionar las solicitudes, una API web usa controladores.</span><span class="sxs-lookup"><span data-stu-id="1b510-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="1b510-107">Los *controladores* de una API web son clases que se derivan de `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="1b510-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="1b510-108">En este artículo se muestra cómo usar controladores para gestionar las solicitudes de API web.</span><span class="sxs-lookup"><span data-stu-id="1b510-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="1b510-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="1b510-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="1b510-110">([Método de descarga](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1b510-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="1b510-111">Clase ControllerBase</span><span class="sxs-lookup"><span data-stu-id="1b510-111">ControllerBase class</span></span>

<span data-ttu-id="1b510-112">Una API web consta de una o varias clases de controlador que se derivan de <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1b510-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1b510-113">La plantilla de proyecto de API web proporciona un controlador de inicio:</span><span class="sxs-lookup"><span data-stu-id="1b510-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="1b510-114">No cree un controlador de API web mediante la derivación de la clase <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="1b510-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="1b510-115">`Controller` se deriva de `ControllerBase` y agrega compatibilidad con vistas, por lo que sirve para gestionar páginas web, no solicitudes de API web.</span><span class="sxs-lookup"><span data-stu-id="1b510-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="1b510-116">Hay una excepción a esta regla: si tiene pensado usar el mismo controlador tanto para las vistas como para las API web, debe derivarlo de `Controller`.</span><span class="sxs-lookup"><span data-stu-id="1b510-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="1b510-117">La clase `ControllerBase` ofrece muchas propiedades y métodos que son útiles para gestionar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="1b510-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="1b510-118">Por ejemplo, `ControllerBase.CreatedAtAction` devuelve un código de estado 201:</span><span class="sxs-lookup"><span data-stu-id="1b510-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="1b510-119">A continuación se muestran algunos ejemplos más de métodos que proporciona `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="1b510-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="1b510-120">Método</span><span class="sxs-lookup"><span data-stu-id="1b510-120">Method</span></span>   |<span data-ttu-id="1b510-121">Notas</span><span class="sxs-lookup"><span data-stu-id="1b510-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="1b510-122">Devuelve el código de estado 400.</span><span class="sxs-lookup"><span data-stu-id="1b510-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="1b510-123">Devuelve el código de estado 404.</span><span class="sxs-lookup"><span data-stu-id="1b510-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="1b510-124">Devuelve un archivo.</span><span class="sxs-lookup"><span data-stu-id="1b510-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="1b510-125">Invoca el [enlace de modelo](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1b510-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="1b510-126">Invoca la [validación de modelos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="1b510-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="1b510-127">Para ver una lista de todos los métodos y propiedades disponibles, consulte <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="1b510-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="1b510-128">Atributos</span><span class="sxs-lookup"><span data-stu-id="1b510-128">Attributes</span></span>

<span data-ttu-id="1b510-129">El espacio de nombres <xref:Microsoft.AspNetCore.Mvc> proporciona atributos que se pueden usar para configurar el comportamiento de los controladores API web y los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="1b510-130">En el ejemplo siguiente se usan atributos para especificar el verbo de acción HTTP admitido y cualquier código de estado HTTP conocido que se pueda devolver:</span><span class="sxs-lookup"><span data-stu-id="1b510-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="1b510-131">Estos son algunos ejemplos más de atributos que están disponibles.</span><span class="sxs-lookup"><span data-stu-id="1b510-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="1b510-132">Atributo</span><span class="sxs-lookup"><span data-stu-id="1b510-132">Attribute</span></span>|<span data-ttu-id="1b510-133">Notas</span><span class="sxs-lookup"><span data-stu-id="1b510-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="1b510-134">Especifica el patrón de dirección URL de un controlador o una acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="1b510-135">Especifica el prefijo y las propiedades que se incluirán en el enlace de modelo.</span><span class="sxs-lookup"><span data-stu-id="1b510-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="1b510-136">Identifica una acción que admite el verbo de acción GET HTTP.</span><span class="sxs-lookup"><span data-stu-id="1b510-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="1b510-137">Especifica los tipos de datos que acepta una acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="1b510-138">Especifica los tipos de datos que devuelve una acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="1b510-139">Para ver una lista que incluye los atributos disponibles, consulte el espacio de nombres <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="1b510-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="1b510-140">Atributo ApiController</span><span class="sxs-lookup"><span data-stu-id="1b510-140">ApiController attribute</span></span>

<span data-ttu-id="1b510-141">El atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) se puede aplicar a una clase de controlador para permitir los siguientes comportamientos específicos de la API:</span><span class="sxs-lookup"><span data-stu-id="1b510-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="1b510-142">Requisito de enrutamiento mediante atributos</span><span class="sxs-lookup"><span data-stu-id="1b510-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="1b510-143">Respuestas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="1b510-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="1b510-144">Inferencia de parámetro de origen de enlace</span><span class="sxs-lookup"><span data-stu-id="1b510-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="1b510-145">Inferencia de solicitud de varios elementos o datos de formulario</span><span class="sxs-lookup"><span data-stu-id="1b510-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="1b510-146">Detalles de problemas de los códigos de estado de error</span><span class="sxs-lookup"><span data-stu-id="1b510-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="1b510-147">La característica *Detalles de problemas de los códigos de estado de error* requiere una [versión de compatibilidad](xref:mvc/compatibility-version) de 2.2 o posterior.</span><span class="sxs-lookup"><span data-stu-id="1b510-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="1b510-148">Las demás características requieren una versión de compatibilidad de 2.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="1b510-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

* [<span data-ttu-id="1b510-149">Requisito de enrutamiento mediante atributos</span><span class="sxs-lookup"><span data-stu-id="1b510-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="1b510-150">Respuestas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="1b510-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="1b510-151">Inferencia de parámetro de origen de enlace</span><span class="sxs-lookup"><span data-stu-id="1b510-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="1b510-152">Inferencia de solicitud de varios elementos o datos de formulario</span><span class="sxs-lookup"><span data-stu-id="1b510-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="1b510-153">Estas características requieren una [versión de compatibilidad](xref:mvc/compatibility-version) de 2.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="1b510-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="1b510-154">Atributo en controladores específicos</span><span class="sxs-lookup"><span data-stu-id="1b510-154">Attribute on specific controllers</span></span>

<span data-ttu-id="1b510-155">El atributo `[ApiController]` puede aplicarse a controladores específicos, como se muestra en el siguiente ejemplo de la plantilla de proyecto:</span><span class="sxs-lookup"><span data-stu-id="1b510-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="1b510-156">Atributo en varios controladores</span><span class="sxs-lookup"><span data-stu-id="1b510-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="1b510-157">Una estrategia para el uso del atributo en más de un controlador consiste en crear una clase personalizada de controlador base anotada con el atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="1b510-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="1b510-158">En el siguiente ejemplo se muestra una clase base personalizada y un controlador que se deriva de ella:</span><span class="sxs-lookup"><span data-stu-id="1b510-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="1b510-159">Atributo en un ensamblado</span><span class="sxs-lookup"><span data-stu-id="1b510-159">Attribute on an assembly</span></span>

<span data-ttu-id="1b510-160">Si la [versión de compatibilidad](xref:mvc/compatibility-version) está establecida en 2.2 o posterior, el atributo `[ApiController]` se puede aplicar a un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="1b510-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="1b510-161">En este contexto, la anotación aplica el comportamiento de API web a todos los controladores del ensamblado.</span><span class="sxs-lookup"><span data-stu-id="1b510-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="1b510-162">No hay ninguna manera de excluir controladores específicos.</span><span class="sxs-lookup"><span data-stu-id="1b510-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="1b510-163">Aplique el atributo de nivel de ensamblado a la declaración del espacio de nombres de alrededor de la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1b510-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="1b510-164">Requisito de enrutamiento mediante atributos</span><span class="sxs-lookup"><span data-stu-id="1b510-164">Attribute routing requirement</span></span>

<span data-ttu-id="1b510-165">El atributo `[ApiController]` convierte el enrutamiento de atributos en un requisito.</span><span class="sxs-lookup"><span data-stu-id="1b510-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="1b510-166">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1b510-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="1b510-167">No es posible acceder a las acciones mediante [rutas convencionales](xref:mvc/controllers/routing#conventional-routing) que hayan definido `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> o <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1b510-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="1b510-168">Las acciones no son accesibles mediante [rutas convencionales](xref:mvc/controllers/routing#conventional-routing) definidas por <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> o <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="1b510-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="1b510-169">Respuestas HTTP 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="1b510-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="1b510-170">El atributo `[ApiController]` hace que los errores de validación de un modelo desencadenen automáticamente una respuesta HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="1b510-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="1b510-171">Por lo tanto, el siguiente código no es necesario en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="1b510-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="1b510-172">ASP.NET Core MVC usa el filtro de acciones <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> para realizar la comprobación anterior.</span><span class="sxs-lookup"><span data-stu-id="1b510-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="1b510-173">Respuesta BadRequest predeterminada</span><span class="sxs-lookup"><span data-stu-id="1b510-173">Default BadRequest response</span></span>

<span data-ttu-id="1b510-174">Con una versión de compatibilidad de 2.1, el tipo de respuesta predeterminado para una respuesta HTTP 400 es <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="1b510-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="1b510-175">El siguiente cuerpo de solicitud es un ejemplo del tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="1b510-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1b510-176">Con una versión de compatibilidad de 2.2 o posterior, el tipo de respuesta predeterminado para una respuesta HTTP 400 es <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="1b510-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="1b510-177">El siguiente cuerpo de solicitud es un ejemplo del tipo serializado:</span><span class="sxs-lookup"><span data-stu-id="1b510-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="1b510-178">Tipo `ValidationProblemDetails`:</span><span class="sxs-lookup"><span data-stu-id="1b510-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="1b510-179">Proporciona un formato de lectura mecánica para especificar errores en las respuestas de la API web.</span><span class="sxs-lookup"><span data-stu-id="1b510-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="1b510-180">Cumple los requisitos de la [especificación RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="1b510-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

<span data-ttu-id="1b510-181">Para que las respuestas automáticas y personalizadas sean coherentes, llame al método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> en lugar de a <xref:System.Web.Http.ApiController.BadRequest%2A>.</span><span class="sxs-lookup"><span data-stu-id="1b510-181">To make automatic and custom responses consistent, call the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> method instead of <xref:System.Web.Http.ApiController.BadRequest%2A>.</span></span> <span data-ttu-id="1b510-182">`ValidationProblem` devuelve un objeto <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> y la respuesta automática.</span><span class="sxs-lookup"><span data-stu-id="1b510-182">`ValidationProblem` returns a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> object as well as the automatic response.</span></span>

### <a name="log-automatic-400-responses"></a><span data-ttu-id="1b510-183">Registro de respuestas 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="1b510-183">Log automatic 400 responses</span></span>

<span data-ttu-id="1b510-184">Vea [Cómo registrar respuestas 400 automáticas sobre errores de validación de modelos (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="1b510-184">See [How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="1b510-185">Deshabilitación de las respuestas 400 automáticas</span><span class="sxs-lookup"><span data-stu-id="1b510-185">Disable automatic 400 response</span></span>

<span data-ttu-id="1b510-186">Para deshabilitar el comportamiento 400 automático, establezca la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> en `true`.</span><span class="sxs-lookup"><span data-stu-id="1b510-186">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="1b510-187">Agregue el código resaltado siguiente en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b510-187">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="1b510-188">Inferencia de parámetro de origen de enlace</span><span class="sxs-lookup"><span data-stu-id="1b510-188">Binding source parameter inference</span></span>

<span data-ttu-id="1b510-189">Un atributo de origen de enlace define la ubicación del valor del parámetro de una acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-189">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="1b510-190">Existen los atributos de origen de enlace siguientes:</span><span class="sxs-lookup"><span data-stu-id="1b510-190">The following binding source attributes exist:</span></span>

|<span data-ttu-id="1b510-191">Atributo</span><span class="sxs-lookup"><span data-stu-id="1b510-191">Attribute</span></span>|<span data-ttu-id="1b510-192">Origen de enlace</span><span class="sxs-lookup"><span data-stu-id="1b510-192">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="1b510-193">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="1b510-193">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="1b510-194">Datos del formulario en el cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="1b510-194">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="1b510-195">Encabezado de la solicitud</span><span class="sxs-lookup"><span data-stu-id="1b510-195">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="1b510-196">Parámetro de la cadena de consulta de la solicitud</span><span class="sxs-lookup"><span data-stu-id="1b510-196">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="1b510-197">Datos de ruta de la solicitud actual</span><span class="sxs-lookup"><span data-stu-id="1b510-197">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="1b510-198">Servicio de solicitud insertado como parámetro de acción</span><span class="sxs-lookup"><span data-stu-id="1b510-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="1b510-199">No use `[FromRoute]` si los valores pueden contener `%2f` (es decir, `/`).</span><span class="sxs-lookup"><span data-stu-id="1b510-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="1b510-200">`%2f` no incluirá el carácter sin escape `/`.</span><span class="sxs-lookup"><span data-stu-id="1b510-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="1b510-201">Use `[FromQuery]` si el valor puede contener `%2f`.</span><span class="sxs-lookup"><span data-stu-id="1b510-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="1b510-202">Sin el atributo `[ApiController]` o los atributos de origen de enlace, como `[FromQuery]`, el entorno de tiempo de ejecución de ASP.NET Core intenta usar el enlazador de modelos de objetos complejos.</span><span class="sxs-lookup"><span data-stu-id="1b510-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="1b510-203">El enlazador de modelos de objetos complejos extrae los datos de los proveedores de valor en un orden definido.</span><span class="sxs-lookup"><span data-stu-id="1b510-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="1b510-204">En el ejemplo siguiente, el atributo `[FromQuery]` indica que el valor del parámetro `discontinuedOnly` se proporciona en la cadena de consulta de la dirección URL de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="1b510-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="1b510-205">El atributo `[ApiController]` aplica reglas de inferencia a los orígenes de datos predeterminados de los parámetros de acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="1b510-206">Estas reglas aplican atributos a los parámetros de acción, lo que ahorra la necesidad de identificar los orígenes de enlace manualmente.</span><span class="sxs-lookup"><span data-stu-id="1b510-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="1b510-207">Las reglas de inferencia de orígenes de enlace se comportan de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="1b510-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="1b510-208">`[FromBody]` se infiere para parámetros de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="1b510-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="1b510-209">La excepción a `[FromBody]` esta regla es cualquier tipo integrado complejo que tenga un significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="1b510-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="1b510-210">El código de inferencia del origen de enlace omite esos tipos especiales.</span><span class="sxs-lookup"><span data-stu-id="1b510-210">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="1b510-211">`[FromForm]` se infiere para los parámetros de acción de tipo <xref:Microsoft.AspNetCore.Http.IFormFile> y <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="1b510-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="1b510-212">No se infiere para los tipos simples o definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="1b510-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="1b510-213">`[FromRoute]` se infiere para cualquier nombre de parámetro de acción que coincida con un parámetro de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="1b510-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="1b510-214">Si varias rutas coinciden con un parámetro de acción, cualquier valor de ruta se considera `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="1b510-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="1b510-215">`[FromQuery]` se infiere para cualquier otro parámetro de acción.</span><span class="sxs-lookup"><span data-stu-id="1b510-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="1b510-216">Notas de la inferencia de FromBody</span><span class="sxs-lookup"><span data-stu-id="1b510-216">FromBody inference notes</span></span>

<span data-ttu-id="1b510-217">En el caso de los tipos simples, como `string` y `int`, `[FromBody]` no se infiere.</span><span class="sxs-lookup"><span data-stu-id="1b510-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="1b510-218">Así pues, para los tipos simples, en los casos en los que quiera utilizar dicha funcionalidad, conviene usar el atributo `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="1b510-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="1b510-219">Cuando una acción tiene más de un parámetro enlazado desde el cuerpo de solicitud, se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="1b510-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="1b510-220">Por ejemplo, todas las firmas de acción siguientes provocan una excepción:</span><span class="sxs-lookup"><span data-stu-id="1b510-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="1b510-221">`[FromBody]` se infiere en ambos porque son tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="1b510-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="1b510-222">El atributo `[FromBody]` en uno se infiere en el otro porque es un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="1b510-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="1b510-223">El atributo `[FromBody]` se infiere en ambos.</span><span class="sxs-lookup"><span data-stu-id="1b510-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="1b510-224">En ASP.NET Core 2.1, los parámetros de tipo de colección, como listas y matrices, se infieren incorrectamente como `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="1b510-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="1b510-225">El atributo `[FromBody]` debe usarse con estos parámetros si van a enlazarse desde el cuerpo de solicitud.</span><span class="sxs-lookup"><span data-stu-id="1b510-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="1b510-226">Este comportamiento se ha corregido en ASP.NET Core 2.2 o posterior, donde se infieren los parámetros de tipo de colección para enlazarse desde el cuerpo de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="1b510-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="1b510-227">Deshabilitación de las reglas de inferencia</span><span class="sxs-lookup"><span data-stu-id="1b510-227">Disable inference rules</span></span>

<span data-ttu-id="1b510-228">Para deshabilitar la inferencia del origen de enlace, establezca <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> en `true`.</span><span class="sxs-lookup"><span data-stu-id="1b510-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="1b510-229">Agregue el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b510-229">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="1b510-230">Inferencia de solicitud de varios elementos o datos de formulario</span><span class="sxs-lookup"><span data-stu-id="1b510-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="1b510-231">El atributo `[ApiController]` aplica una regla de inferencia cuando se anota un parámetro de acción con el atributo [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute).</span><span class="sxs-lookup"><span data-stu-id="1b510-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="1b510-232">Se infiere el tipo de contenido de la solicitud `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="1b510-232">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="1b510-233">Para deshabilitar el comportamiento predeterminado, establezca la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> en `true` en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b510-233">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="1b510-234">Detalles de problemas de los códigos de estado de error</span><span class="sxs-lookup"><span data-stu-id="1b510-234">Problem details for error status codes</span></span>

<span data-ttu-id="1b510-235">Cuando la versión de compatibilidad es 2.2 o posterior, MVC transforma un resultado de error (un resultado con el código de estado 400 o superior) en un resultado con <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="1b510-235">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="1b510-236">El tipo `ProblemDetails` se basa en la [especificación RFC 7807](https://tools.ietf.org/html/rfc7807) para proporcionar detalles de error de lectura mecánica en una respuesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="1b510-236">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="1b510-237">Observe el código siguiente en una acción de controlador:</span><span class="sxs-lookup"><span data-stu-id="1b510-237">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="1b510-238">El método `NotFound` genera un código de estado HTTP 404 con un cuerpo `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="1b510-238">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="1b510-239">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1b510-239">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="1b510-240">Deshabilitación de la respuesta ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="1b510-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="1b510-241">La creación automática de `ProblemDetails` para los códigos de estado de error está deshabilitada cuando la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> está establecida en `true`.</span><span class="sxs-lookup"><span data-stu-id="1b510-241">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="1b510-242">Agregue el siguiente código en `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1b510-242">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="1b510-243">Definición de tipos de contenido de la solicitud compatibles con el atributo [Consumes]</span><span class="sxs-lookup"><span data-stu-id="1b510-243">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="1b510-244">De forma predeterminada, una acción admite todos los tipos de contenido de la solicitud disponibles.</span><span class="sxs-lookup"><span data-stu-id="1b510-244">By default, an action supports all available request content types.</span></span> <span data-ttu-id="1b510-245">Por ejemplo, si una aplicación está configurada para admitir [formateadores de entrada](xref:mvc/models/model-binding#input-formatters) JSON y XML, una acción admite varios tipos de contenido, incluidos `application/json` y `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="1b510-245">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="1b510-246">El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles.</span><span class="sxs-lookup"><span data-stu-id="1b510-246">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="1b510-247">Aplique el atributo `[Consumes]` a una acción o controlador, especificando uno o varios tipos de contenido:</span><span class="sxs-lookup"><span data-stu-id="1b510-247">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="1b510-248">En el código anterior, la acción `CreateProduct` especifica el tipo de contenido `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="1b510-248">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="1b510-249">Las solicitudes enrutadas a esta acción deben especificar un encabezado `Content-Type` de `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="1b510-249">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="1b510-250">Las solicitudes que no especifican un encabezado `Content-Type` de `application/xml` generan una respuesta [415 Tipo de medio no compatible](https://developer.mozilla.org/docs/Web/HTTP/Status/415).</span><span class="sxs-lookup"><span data-stu-id="1b510-250">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="1b510-251">El atributo `[Consumes]` también permite que una acción influya en su selección en función del tipo de contenido de una solicitud entrante aplicando una restricción de tipo.</span><span class="sxs-lookup"><span data-stu-id="1b510-251">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="1b510-252">Considere el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1b510-252">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="1b510-253">En el código anterior, `ConsumesController` se configura para controlar las solicitudes enviadas a la dirección URL `https://localhost:5001/api/Consumes`.</span><span class="sxs-lookup"><span data-stu-id="1b510-253">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="1b510-254">Las dos acciones del controlador, `PostJson` y `PostForm`, controlan las solicitudes POST con la misma dirección URL.</span><span class="sxs-lookup"><span data-stu-id="1b510-254">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="1b510-255">Sin el atributo `[Consumes]` que aplica una restricción de tipo, se produce una excepción de coincidencia ambigua.</span><span class="sxs-lookup"><span data-stu-id="1b510-255">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="1b510-256">El atributo `[Consumes]` se aplica a ambas acciones.</span><span class="sxs-lookup"><span data-stu-id="1b510-256">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="1b510-257">La acción `PostJson` controla las solicitudes enviadas con un encabezado `Content-Type` de `application/json`.</span><span class="sxs-lookup"><span data-stu-id="1b510-257">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="1b510-258">La acción `PostForm` controla las solicitudes enviadas con un encabezado `Content-Type` de `application/x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="1b510-258">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="1b510-259">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="1b510-259">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [<span data-ttu-id="1b510-260">Microsoft Learn: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b510-260">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
