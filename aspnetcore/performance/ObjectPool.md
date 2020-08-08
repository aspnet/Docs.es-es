---
title: Reutilización de objetos con ObjectPool en ASP.NET Core
author: rick-anderson
description: Sugerencias para aumentar el rendimiento en ASP.NET Core aplicaciones mediante ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 1f57bc4662296333b3d2c659c057230548541b91
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020410"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Reutilización de objetos con ObjectPool en ASP.NET Core

Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)y [Günther Foidl](https://github.com/gfoidl)

<xref:Microsoft.Extensions.ObjectPool>forma parte de la infraestructura de ASP.NET Core que permite mantener un grupo de objetos en memoria para su reutilización, en lugar de permitir que los objetos se recopilen como elementos no utilizados.

Es posible que desee usar el grupo de objetos si los objetos que se están administrando son:

- Costosa asignación/inicialización.
- Representan algún recurso limitado.
- Se utiliza de forma predecible y frecuente.

Por ejemplo, el marco de ASP.NET Core usa el grupo de objetos en algunos lugares para reutilizar <xref:System.Text.StringBuilder> las instancias. `StringBuilder`asigna y administra sus propios búferes para contener datos de caracteres. ASP.NET Core usa `StringBuilder` con regularidad para implementar características y su reutilización proporciona una ventaja de rendimiento.

La agrupación de objetos no siempre mejora el rendimiento:

- A menos que el costo de inicialización de un objeto sea alto, suele ser más lento obtener el objeto del grupo.
- No se anula la asignación de los objetos administrados por el grupo hasta que no se haya anulado la asignación del grupo.

Use la agrupación de objetos solo después de recopilar datos de rendimiento mediante escenarios realistas para la aplicación o biblioteca.

::: moniker range="< aspnetcore-3.0"
**ADVERTENCIA: `ObjectPool` no implementa `IDisposable` . No se recomienda su uso con tipos que necesitan la eliminación.** `ObjectPool`en ASP.NET Core 3,0 y versiones posteriores admiten `IDisposable` .
::: moniker-end

**Nota: ObjectPool no impone ningún límite en cuanto al número de objetos que asignará, sino que establece un límite en el número de objetos que se conservarán.**

## <a name="concepts"></a>Conceptos

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-la abstracción básica del grupo de objetos. Se utiliza para obtener y devolver objetos.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implementar esto para personalizar cómo se crea un objeto y cómo se *restablece* cuando se devuelve al grupo. Se puede pasar a un grupo de objetos que se construye directamente.... DE

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>actúa como un generador para crear grupos de objetos.
<!-- REview, there is no ObjectPoolProvider<T> -->

El ObjectPool se puede usar en una aplicación de varias maneras:

* Crear una instancia de un grupo.
* Registrar un grupo en la [inserción de dependencias](xref:fundamentals/dependency-injection) (di) como una instancia de.
* Registrar `ObjectPoolProvider<>` en di y usarlo como generador.

## <a name="how-to-use-objectpool"></a>Cómo usar ObjectPool

Llame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> a para obtener un objeto y <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> devolver el objeto.  No es necesario que devuelva todos los objetos. Si no devuelve un objeto, se recolectará como elemento no utilizado.

::: moniker range=">= aspnetcore-3.0"
Cuando <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> se usa e `T` implementa `IDisposable` :

* Los elementos que ***no*** se devuelven al grupo se eliminarán.
* Cuando DI elimina el grupo, se eliminan todos los elementos del grupo.

Nota: una vez eliminado el Grupo:

* La llamada a `Get` produce una excepción `ObjectDisposedException` .
* `return`desecha el elemento determinado.

::: moniker-end

## <a name="objectpool-sample"></a>Ejemplo de ObjectPool

El código siguiente:

* Agrega `ObjectPoolProvider` al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) (di).
* Agrega y configura `ObjectPool<StringBuilder>` en el contenedor de di.
* Agrega el `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

El código siguiente implementa`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
