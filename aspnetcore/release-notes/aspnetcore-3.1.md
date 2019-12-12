---
title: Novedades de ASP.NET Core 3.1
author: rick-anderson
description: Obtenga información sobre las nuevas características de ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 634c6937089a0a0fe1f862a83771aff65a1f8418
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778848"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="1abab-103">Novedades de ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="1abab-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="1abab-104">En este artículo se resaltan los cambios más importantes de ASP.NET Core 3.1, con vínculos a la documentación pertinente.</span><span class="sxs-lookup"><span data-stu-id="1abab-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="1abab-105">Compatibilidad de clases parciales con componentes de Razor</span><span class="sxs-lookup"><span data-stu-id="1abab-105">Partial class support for Razor components</span></span>

<span data-ttu-id="1abab-106">Ahora los componentes de Razor se generan como clases parciales.</span><span class="sxs-lookup"><span data-stu-id="1abab-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="1abab-107">El código de un componente de Razor se puede escribir con un archivo de código subyacente definido como una clase parcial en lugar de definir todo el código del componente en un solo archivo.</span><span class="sxs-lookup"><span data-stu-id="1abab-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="1abab-108">Para más información, vea [Compatibilidad de clases parciales](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="1abab-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a><span data-ttu-id="1abab-109">El Asistente de etiquetas de componente de Blazor y transferencia de parámetros a componentes de nivel superior</span><span class="sxs-lookup"><span data-stu-id="1abab-109">Blazor Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="1abab-110">En Blazor con ASP.NET Core 3.0, los componentes se representaban en páginas y vistas mediante una aplicación auxiliar de HTML (`Html.RenderComponentAsync`).</span><span class="sxs-lookup"><span data-stu-id="1abab-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="1abab-111">En ASP.NET Core 3.1, un componente se representa desde una página o vista con el nuevo asistente de etiquetas de componente:</span><span class="sxs-lookup"><span data-stu-id="1abab-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="1abab-112">La aplicación auxiliar de HTML sigue siendo compatible con ASP.NET Core 3.1, pero se recomienda el asistente de etiquetas de componente.</span><span class="sxs-lookup"><span data-stu-id="1abab-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="1abab-113">Ahora, las aplicaciones de Blazor Server pueden pasar parámetros a componentes de nivel superior durante la representación inicial.</span><span class="sxs-lookup"><span data-stu-id="1abab-113">Blazor Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="1abab-114">Anteriormente, solo se podían pasar parámetros a un componente de nivel superior por medio de [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="1abab-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="1abab-115">Con esta versión, se admiten tanto [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) como [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered).</span><span class="sxs-lookup"><span data-stu-id="1abab-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="1abab-116">Los valores de parámetro especificados se serializan como JSON y se incluyen en la respuesta inicial.</span><span class="sxs-lookup"><span data-stu-id="1abab-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="1abab-117">Por ejemplo, se puede realizar la representación previa de un componente `Counter` con una cantidad de incremento (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="1abab-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="1abab-118">Para más información, vea [Integración de componentes en aplicaciones de Razor Pages y MVC](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="1abab-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="1abab-119">Compatibilidad con las colas compartidas en HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="1abab-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="1abab-120">[HTTP.sys](xref:fundamentals/servers/httpsys) admite la creación de colas de solicitudes anónimas.</span><span class="sxs-lookup"><span data-stu-id="1abab-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="1abab-121">En ASP.NET Core 3.1, se ha agregado la capacidad de crear o adjuntar a una cola de solicitudes de HTTP.sys con nombre existente.</span><span class="sxs-lookup"><span data-stu-id="1abab-121">In ASP.NET Core 3.1, we’ve added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="1abab-122">La operación de crear o adjuntar a una cola de solicitudes de HTTP.sys con nombre existente habilita escenarios en los que el proceso del controlador HTTP.Sys propietario de la cola es independiente del proceso del cliente de escucha.</span><span class="sxs-lookup"><span data-stu-id="1abab-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.Sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="1abab-123">Esta independencia permite conservar las conexiones existentes y las solicitudes en cola entre los reinicios del proceso del cliente de escucha:</span><span class="sxs-lookup"><span data-stu-id="1abab-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="1abab-124">Cambios importantes en las cookies de SameSite</span><span class="sxs-lookup"><span data-stu-id="1abab-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="1abab-125">El comportamiento de las cookies de SameSite ha cambiado para reflejar los próximos cambios del explorador.</span><span class="sxs-lookup"><span data-stu-id="1abab-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="1abab-126">Esto puede afectar a escenarios de autenticación como AzureAd, OpenIdConnect o WsFederation.</span><span class="sxs-lookup"><span data-stu-id="1abab-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="1abab-127">Para más información, consulte <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="1abab-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="1abab-128">Impedir acciones predeterminadas para eventos en aplicaciones Blazor</span><span class="sxs-lookup"><span data-stu-id="1abab-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="1abab-129">Use el atributo de directiva `@on{EVENT}:preventDefault` para evitar la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="1abab-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="1abab-130">En el ejemplo siguiente, se impide la acción predeterminada de mostrar el carácter de la clave en el cuadro de texto:</span><span class="sxs-lookup"><span data-stu-id="1abab-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="1abab-131">Para más información, vea [Impedir acciones predeterminadas](xref:blazor/components#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="1abab-131">For more information, see [Prevent default actions](xref:blazor/components#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="1abab-132">Detener la propagación de eventos en aplicaciones Blazor</span><span class="sxs-lookup"><span data-stu-id="1abab-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="1abab-133">Use el atributo de directiva `@on{EVENT}:stopPropagation` para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="1abab-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="1abab-134">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento `<div>` secundario se propaguen al elemento `<div>` principal:</span><span class="sxs-lookup"><span data-stu-id="1abab-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="1abab-135">Para más información, vea [Detener la propagación de eventos](xref:blazor/components#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="1abab-135">For more information, see [Stop event propagation](xref:blazor/components#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="1abab-136">Errores detallados durante el desarrollo de aplicaciones Blazor</span><span class="sxs-lookup"><span data-stu-id="1abab-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="1abab-137">Cuando una aplicación Blazor no funciona correctamente durante el desarrollo, recibir información detallada del error de la aplicación ayuda a solucionar el problema.</span><span class="sxs-lookup"><span data-stu-id="1abab-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="1abab-138">Cuando se produce un error, en las aplicaciones Blazor se muestra una barra dorada en la parte inferior de la pantalla:</span><span class="sxs-lookup"><span data-stu-id="1abab-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="1abab-139">Durante el desarrollo, la barra dorada le dirige a la consola del explorador, donde puede ver la excepción.</span><span class="sxs-lookup"><span data-stu-id="1abab-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="1abab-140">En producción, la barra dorada informa al usuario de que se ha producido un error y recomienda actualizar el explorador.</span><span class="sxs-lookup"><span data-stu-id="1abab-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="1abab-141">Para más información, vea [Errores detallados durante el desarrollo](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="1abab-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>