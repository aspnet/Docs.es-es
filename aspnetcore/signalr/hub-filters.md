---
title: Usar filtros de Hub en ASP.NET Core SignalR
author: brecon
description: Aprenda a usar filtros de Hub en ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052726"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>Usar filtros de Hub en ASP.NET Core SignalR

Filtros de Hub:

* Están disponibles en ASP.NET Core 5,0 o posterior.
* Permita que la lógica se ejecute antes y después de que los clientes invoquen los métodos de concentrador.

En este artículo se proporcionan instrucciones para escribir y usar filtros de Hub.

## <a name="configure-hub-filters"></a>Configuración de filtros de Hub

Los filtros de concentrador se pueden aplicar globalmente o por tipo de concentrador. El orden en que se agregan los filtros es el orden en el que se ejecutan los filtros. Los filtros globales del concentrador se ejecutan antes que los filtros del concentrador local.

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

Se puede Agregar un filtro de concentrador de una de las siguientes maneras:

* Agregue un filtro por tipo concreto:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Esto se resolverá a partir de la inserción de dependencias (DI) o del tipo activado.

* Agregar un filtro por tipo en tiempo de ejecución:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Esto se resolverá desde DI o el tipo activado.

* Agregue un filtro por instancia:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Esta instancia se usará como singleton. Todas las invocaciones de método del concentrador usarán la misma instancia.

Los filtros de concentrador se crean y se eliminan por invocación de concentrador. Si desea almacenar el estado global en el filtro o en ningún Estado, agregue el tipo de filtro de concentrador a DI como singleton para mejorar el rendimiento. También puede Agregar el filtro como una instancia si es posible.

## <a name="create-hub-filters"></a>Crear filtros de concentrador

Cree un filtro declarando una clase que herede de `IHubFilter` y agregue el `InvokeMethodAsync` método. También hay `OnConnectedAsync` y `OnDisconnectedAsync` que se pueden implementar opcionalmente para encapsular los `OnConnectedAsync` métodos de `OnDisconnectedAsync` concentrador y respectivamente.

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
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
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

Los filtros son muy similares a middleware. El `next` método invoca el siguiente filtro. El filtro final invocará el método de concentrador. Los filtros también pueden almacenar el resultado de la espera y la ejecución de la `next` lógica después de haber llamado al método de concentrador antes de devolver el resultado de `next` .

Para omitir una invocación de método de concentrador en un filtro, inicie una excepción de tipo en `HubException` lugar de llamar a `next` . El cliente recibirá un error si se espera un resultado.

## <a name="use-hub-filters"></a>Usar filtros de Hub

Al escribir la lógica de filtro, intente convertirla en genérica mediante el uso de atributos en métodos de concentrador en lugar de comprobar los nombres de método de concentrador.

Considere un filtro que comprobará un argumento de método de concentrador para frases prohibidas y reemplazará las frases con las que encuentre `***` .
En este ejemplo, suponga que `LanguageFilterAttribute` se define una clase. La clase tiene una propiedad denominada `FilterArgument` que se puede establecer cuando se usa el atributo.

1. Coloque el atributo en el método de concentrador que tiene un argumento de cadena que se va a limpiar:

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

1. Defina un filtro de concentrador para comprobar el atributo y reemplazar las frases prohibidas en un argumento de método de concentrador con `**_` :

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

1. Registre el filtro del concentrador en el `Startup.ConfigureServices` método. Para evitar la reinicialización de la lista de frases prohibidas para cada invocación, el filtro del concentrador se registra como singleton:

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

## <a name="the-hubinvocationcontext-object"></a>El objeto HubInvocationContext

`HubInvocationContext`Contiene información para la invocación del método del concentrador actual.

| Propiedad | Descripción | Tipo |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contiene información sobre la conexión. | `HubCallerContext` |
| `Hub` | La instancia del concentrador que se usa para esta invocación de método de concentrador. | `Hub` |
| `HubMethodName` | Nombre del método de concentrador que se va a invocar. | `string` |
| `HubMethodArguments` | Lista de argumentos que se pasan al método de concentrador. | `IReadOnlyList<string>` |
| `ServiceProvider` | Proveedor de servicios con ámbito para esta invocación de método de concentrador. | `IServiceProvider` |
| `HubMethod` | Información del método de concentrador. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>El objeto HubLifetimeContext

`HubLifetimeContext`Contiene información para los `OnConnectedAsync` métodos de `OnDisconnectedAsync` concentrador y.

| Propiedad | Descripción | Tipo |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Contiene información sobre la conexión. | `HubCallerContext` |
| `Hub` | La instancia del concentrador que se usa para esta invocación de método de concentrador. | `Hub` |
| `ServiceProvider` | Proveedor de servicios con ámbito para esta invocación de método de concentrador. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorización y filtros

[Autorice atributos en métodos de concentrador](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) que se ejecuten antes que los filtros de Hub.
