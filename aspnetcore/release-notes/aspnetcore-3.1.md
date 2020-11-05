---
title: Novedades de ASP.NET Core 3.1
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: aspnetcore-3.1
ms.openlocfilehash: 15240df978d9389af08030521adaf491f81d3a77
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051179"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="4738b-103">Novedades de ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="4738b-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="4738b-104">En este artículo se resaltan los cambios más importantes de ASP.NET Core 3.1, con vínculos a la documentación pertinente.</span><span class="sxs-lookup"><span data-stu-id="4738b-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-no-locrazor-components"></a><span data-ttu-id="4738b-105">Compatibilidad de clases parciales con componentes de :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="4738b-105">Partial class support for :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="4738b-106">Ahora los componentes de :::no-loc(Razor)::: se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="4738b-106">:::no-loc(Razor)::: components are now generated as partial classes.</span></span> <span data-ttu-id="4738b-107">El código de un componente de :::no-loc(Razor)::: se puede escribir con un archivo de código subyacente definido como una clase parcial en lugar de definir todo el código del componente en un solo archivo.</span><span class="sxs-lookup"><span data-stu-id="4738b-107">Code for a :::no-loc(Razor)::: component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="4738b-108">Para más información, vea [Compatibilidad de clases parciales](xref:blazor/components/index#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="4738b-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a><span data-ttu-id="4738b-109">El Asistente de etiquetas de componente de :::no-loc(Blazor)::: y transferencia de parámetros a componentes de nivel superior</span><span class="sxs-lookup"><span data-stu-id="4738b-109">:::no-loc(Blazor)::: Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="4738b-110">En :::no-loc(Blazor)::: con ASP.NET Core 3.0, los componentes se representaban en páginas y vistas mediante una aplicación auxiliar de HTML (`Html.RenderComponentAsync`).</span><span class="sxs-lookup"><span data-stu-id="4738b-110">In :::no-loc(Blazor)::: with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="4738b-111">En ASP.NET Core 3.1, un componente se representa desde una página o vista con el nuevo asistente de etiquetas de componente:</span><span class="sxs-lookup"><span data-stu-id="4738b-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4738b-112">La aplicación auxiliar de HTML sigue siendo compatible con ASP.NET Core 3.1, pero se recomienda el asistente de etiquetas de componente.</span><span class="sxs-lookup"><span data-stu-id="4738b-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="4738b-113">Ahora, las aplicaciones :::no-loc(Blazor Server)::: pueden pasar parámetros a componentes de nivel superior durante la representación inicial.</span><span class="sxs-lookup"><span data-stu-id="4738b-113">:::no-loc(Blazor Server)::: apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="4738b-114">Anteriormente, solo se podían pasar parámetros a un componente de nivel superior por medio de [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="4738b-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="4738b-115">Con esta versión, se admiten tanto [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) como [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered).</span><span class="sxs-lookup"><span data-stu-id="4738b-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="4738b-116">Los valores de parámetro especificados se serializan como JSON y se incluyen en la respuesta inicial.</span><span class="sxs-lookup"><span data-stu-id="4738b-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="4738b-117">Por ejemplo, se puede realizar la representación previa de un componente `Counter` con una cantidad de incremento (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="4738b-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="4738b-118">Para obtener más información, vea [Integración de componentes en aplicaciones :::no-loc(Razor)::: Pages y MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="4738b-118">For more information, see [Integrate components into :::no-loc(Razor)::: Pages and MVC apps](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="4738b-119">Compatibilidad con las colas compartidas en HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4738b-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="4738b-120">[HTTP.sys](xref:fundamentals/servers/httpsys) admite la creación de colas de solicitudes anónimas.</span><span class="sxs-lookup"><span data-stu-id="4738b-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="4738b-121">En ASP.NET Core 3.1, hemos agregado la capacidad de crear o adjuntar contenido a una cola de solicitudes existente de HTTP.sys con nombre.</span><span class="sxs-lookup"><span data-stu-id="4738b-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="4738b-122">La operación de crear o adjuntar contenido a una cola de solicitudes existente de HTTP.sys con nombre habilita escenarios en los que el proceso del controlador HTTP.Sys propietario de la cola es independiente del proceso del cliente de escucha.</span><span class="sxs-lookup"><span data-stu-id="4738b-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="4738b-123">Esta independencia permite conservar las conexiones existentes y las solicitudes en cola entre los reinicios del proceso del cliente de escucha:</span><span class="sxs-lookup"><span data-stu-id="4738b-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a><span data-ttu-id="4738b-124">Cambios importantes en las :::no-loc(cookie):::s de SameSite</span><span class="sxs-lookup"><span data-stu-id="4738b-124">Breaking changes for SameSite :::no-loc(cookie):::s</span></span>

<span data-ttu-id="4738b-125">El comportamiento de las :::no-loc(cookie):::s de SameSite ha cambiado para reflejar los próximos cambios del explorador.</span><span class="sxs-lookup"><span data-stu-id="4738b-125">The behavior of SameSite :::no-loc(cookie):::s has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="4738b-126">Esto puede afectar a escenarios de autenticación como AzureAd, OpenIdConnect o WsFederation.</span><span class="sxs-lookup"><span data-stu-id="4738b-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="4738b-127">Para obtener más información, vea <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="4738b-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a><span data-ttu-id="4738b-128">Impedir acciones predeterminadas para eventos en aplicaciones :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="4738b-128">Prevent default actions for events in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="4738b-129">Use el atributo de directiva `@on{EVENT}:preventDefault` para evitar la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="4738b-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="4738b-130">En el ejemplo siguiente, se impide la acción predeterminada de mostrar el carácter de la clave en el cuadro de texto:</span><span class="sxs-lookup"><span data-stu-id="4738b-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="4738b-131">Para más información, vea [Impedir acciones predeterminadas](xref:blazor/components/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="4738b-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-no-locblazor-apps"></a><span data-ttu-id="4738b-132">Detener la propagación de eventos en aplicaciones :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="4738b-132">Stop event propagation in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="4738b-133">Use el atributo de directiva `@on{EVENT}:stopPropagation` para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="4738b-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="4738b-134">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento `<div>` secundario se propaguen al elemento `<div>` principal:</span><span class="sxs-lookup"><span data-stu-id="4738b-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="4738b-135">Para más información, vea [Detener la propagación de eventos](xref:blazor/components/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="4738b-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-no-locblazor-app-development"></a><span data-ttu-id="4738b-136">Errores detallados durante el desarrollo de aplicaciones :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="4738b-136">Detailed errors during :::no-loc(Blazor)::: app development</span></span>

<span data-ttu-id="4738b-137">Cuando una aplicación :::no-loc(Blazor)::: no funciona correctamente durante el desarrollo, recibir información detallada del error de la aplicación ayuda a solucionar el problema.</span><span class="sxs-lookup"><span data-stu-id="4738b-137">When a :::no-loc(Blazor)::: app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="4738b-138">Cuando se produce un error, en las aplicaciones :::no-loc(Blazor)::: se muestra una barra dorada en la parte inferior de la pantalla:</span><span class="sxs-lookup"><span data-stu-id="4738b-138">When an error occurs, :::no-loc(Blazor)::: apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="4738b-139">Durante el desarrollo, la barra dorada le dirige a la consola del explorador, donde puede ver la excepción.</span><span class="sxs-lookup"><span data-stu-id="4738b-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="4738b-140">En producción, la barra dorada informa al usuario de que se ha producido un error y recomienda actualizar el explorador.</span><span class="sxs-lookup"><span data-stu-id="4738b-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="4738b-141">Para más información, vea [Errores detallados durante el desarrollo](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="4738b-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
