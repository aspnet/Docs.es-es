---
title: 'Usar filtros de Hub en ASP.NET Core SignalR'
author: brecon
description: 'Aprenda a usar filtros de Hub en ASP.NET Core SignalR .'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/hub-filters
ms.openlocfilehash: 9b131d8ec13204525f39263afaf506e336373a7c
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634579"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="f6776-103">Usar filtros de Hub en ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f6776-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="f6776-104">Filtros de Hub:</span><span class="sxs-lookup"><span data-stu-id="f6776-104">Hub filters:</span></span>

* <span data-ttu-id="f6776-105">Están disponibles en ASP.NET Core 5,0 o posterior.</span><span class="sxs-lookup"><span data-stu-id="f6776-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="f6776-106">Permita que la lógica se ejecute antes y después de que los clientes invoquen los métodos de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="f6776-107">En este artículo se proporcionan instrucciones para escribir y usar filtros de Hub.</span><span class="sxs-lookup"><span data-stu-id="f6776-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="f6776-108">Configuración de filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="f6776-108">Configure hub filters</span></span>

<span data-ttu-id="f6776-109">Los filtros de concentrador se pueden aplicar globalmente o por tipo de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="f6776-110">El orden en que se agregan los filtros es el orden en el que se ejecutan los filtros.</span><span class="sxs-lookup"><span data-stu-id="f6776-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="f6776-111">Los filtros globales del concentrador se ejecutan antes que los filtros del concentrador local.</span><span class="sxs-lookup"><span data-stu-id="f6776-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="f6776-112">Se puede Agregar un filtro de concentrador de una de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="f6776-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="f6776-113">Agregue un filtro por tipo concreto:</span><span class="sxs-lookup"><span data-stu-id="f6776-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="f6776-114">Esto se resolverá a partir de la inserción de dependencias (DI) o del tipo activado.</span><span class="sxs-lookup"><span data-stu-id="f6776-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="f6776-115">Agregar un filtro por tipo en tiempo de ejecución:</span><span class="sxs-lookup"><span data-stu-id="f6776-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="f6776-116">Esto se resolverá desde DI o el tipo activado.</span><span class="sxs-lookup"><span data-stu-id="f6776-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="f6776-117">Agregue un filtro por instancia:</span><span class="sxs-lookup"><span data-stu-id="f6776-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="f6776-118">Esta instancia se usará como singleton.</span><span class="sxs-lookup"><span data-stu-id="f6776-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="f6776-119">Todas las invocaciones de método del concentrador usarán la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="f6776-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="f6776-120">Los filtros de concentrador se crean y se eliminan por invocación de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="f6776-121">Si desea almacenar el estado global en el filtro o en ningún Estado, agregue el tipo de filtro de concentrador a DI como singleton para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f6776-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="f6776-122">También puede Agregar el filtro como una instancia si es posible.</span><span class="sxs-lookup"><span data-stu-id="f6776-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="f6776-123">Crear filtros de concentrador</span><span class="sxs-lookup"><span data-stu-id="f6776-123">Create hub filters</span></span>

<span data-ttu-id="f6776-124">Cree un filtro declarando una clase que herede de `IHubFilter` y agregue el `InvokeMethodAsync` método.</span><span class="sxs-lookup"><span data-stu-id="f6776-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="f6776-125">También hay `OnConnectedAsync` y `OnDisconnectedAsync` que se pueden implementar opcionalmente para encapsular los `OnConnectedAsync` métodos de `OnDisconnectedAsync` concentrador y respectivamente.</span><span class="sxs-lookup"><span data-stu-id="f6776-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}': {ex}");
            throw;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="f6776-126">Los filtros son muy similares a middleware.</span><span class="sxs-lookup"><span data-stu-id="f6776-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="f6776-127">El `next` método invoca el siguiente filtro.</span><span class="sxs-lookup"><span data-stu-id="f6776-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="f6776-128">El filtro final invocará el método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="f6776-129">Los filtros también pueden almacenar el resultado de la espera y la ejecución de la `next` lógica después de haber llamado al método de concentrador antes de devolver el resultado de `next` .</span><span class="sxs-lookup"><span data-stu-id="f6776-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="f6776-130">Para omitir una invocación de método de concentrador en un filtro, inicie una excepción de tipo en `HubException` lugar de llamar a `next` .</span><span class="sxs-lookup"><span data-stu-id="f6776-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="f6776-131">El cliente recibirá un error si se espera un resultado.</span><span class="sxs-lookup"><span data-stu-id="f6776-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="f6776-132">Usar filtros de Hub</span><span class="sxs-lookup"><span data-stu-id="f6776-132">Use hub filters</span></span>

<span data-ttu-id="f6776-133">Al escribir la lógica de filtro, intente convertirla en genérica mediante el uso de atributos en métodos de concentrador en lugar de comprobar los nombres de método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="f6776-134">Considere un filtro que comprobará un argumento de método de concentrador para frases prohibidas y reemplazará las frases con las que encuentre `***` .</span><span class="sxs-lookup"><span data-stu-id="f6776-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="f6776-135">En este ejemplo, suponga que `LanguageFilterAttribute` se define una clase.</span><span class="sxs-lookup"><span data-stu-id="f6776-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="f6776-136">La clase tiene una propiedad denominada `FilterArgument` que se puede establecer cuando se usa el atributo.</span><span class="sxs-lookup"><span data-stu-id="f6776-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="f6776-137">Coloque el atributo en el método de concentrador que tiene un argumento de cadena que se va a limpiar:</span><span class="sxs-lookup"><span data-stu-id="f6776-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="f6776-138">Defina un filtro de concentrador para comprobar el atributo y reemplazar las frases prohibidas en un argumento de método de concentrador con `**_` :</span><span class="sxs-lookup"><span data-stu-id="f6776-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="f6776-139">Registre el filtro del concentrador en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="f6776-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f6776-140">Para evitar la reinicialización de la lista de frases prohibidas para cada invocación, el filtro del concentrador se registra como singleton:</span><span class="sxs-lookup"><span data-stu-id="f6776-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="f6776-141">El objeto HubInvocationContext</span><span class="sxs-lookup"><span data-stu-id="f6776-141">The HubInvocationContext object</span></span>

<span data-ttu-id="f6776-142">`HubInvocationContext`Contiene información para la invocación del método del concentrador actual.</span><span class="sxs-lookup"><span data-stu-id="f6776-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="f6776-143">Propiedad</span><span class="sxs-lookup"><span data-stu-id="f6776-143">Property</span></span> | <span data-ttu-id="f6776-144">Descripción</span><span class="sxs-lookup"><span data-stu-id="f6776-144">Description</span></span> | <span data-ttu-id="f6776-145">Tipo</span><span class="sxs-lookup"><span data-stu-id="f6776-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f6776-146">`HubCallerContext`Contiene información sobre la conexión.</span><span class="sxs-lookup"><span data-stu-id="f6776-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f6776-147">La instancia del concentrador que se usa para esta invocación de método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="f6776-148">Nombre del método de concentrador que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="f6776-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="f6776-149">Lista de argumentos que se pasan al método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="f6776-150">Proveedor de servicios con ámbito para esta invocación de método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="f6776-151">Información del método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="f6776-152">El objeto HubLifetimeContext</span><span class="sxs-lookup"><span data-stu-id="f6776-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="f6776-153">`HubLifetimeContext`Contiene información para los `OnConnectedAsync` métodos de `OnDisconnectedAsync` concentrador y.</span><span class="sxs-lookup"><span data-stu-id="f6776-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="f6776-154">Propiedad</span><span class="sxs-lookup"><span data-stu-id="f6776-154">Property</span></span> | <span data-ttu-id="f6776-155">Descripción</span><span class="sxs-lookup"><span data-stu-id="f6776-155">Description</span></span> | <span data-ttu-id="f6776-156">Tipo</span><span class="sxs-lookup"><span data-stu-id="f6776-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f6776-157">`HubCallerContext`Contiene información sobre la conexión.</span><span class="sxs-lookup"><span data-stu-id="f6776-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f6776-158">La instancia del concentrador que se usa para esta invocación de método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="f6776-159">Proveedor de servicios con ámbito para esta invocación de método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="f6776-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="f6776-160">Autorización y filtros</span><span class="sxs-lookup"><span data-stu-id="f6776-160">Authorization and filters</span></span>

<span data-ttu-id="f6776-161">[Autorice atributos en métodos de concentrador](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) que se ejecuten antes que los filtros de Hub.</span><span class="sxs-lookup"><span data-stu-id="f6776-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
