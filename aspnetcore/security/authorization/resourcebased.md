---
title: Autorización basada en recursos en ASP.NET Core
author: scottaddie
description: Obtenga información sobre cómo implementar la autorización basada en recursos en una aplicación ASP.NET Core cuando un atributo Authorize no sea suficiente.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: bb47f3452d29dfeea0e4d3c4a9c22a06869a3fe7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631360"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Autorización basada en recursos en ASP.NET Core

La estrategia de autorización depende del recurso al que se tiene acceso. Considere un documento que tiene una propiedad Author. Solo el autor puede actualizar el documento. Por consiguiente, el documento se debe recuperar del almacén de datos antes de que se pueda realizar la evaluación de la autorización.

La evaluación de atributos se produce antes del enlace de datos y antes de la ejecución del controlador de páginas o la acción que carga el documento. Por estos motivos, la autorización declarativa con un `[Authorize]` atributo no basta. En su lugar, puede invocar un método de autorización personalizado que &mdash; sea un estilo conocido como *autorización imperativa*.

::: moniker range=">= aspnetcore-3.0"
[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([cómo descargarlo](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([cómo descargarlo](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([cómo descargarlo](xref:index#how-to-download-a-sample)).
::: moniker-end

[Creación de una aplicación ASP.net Core con datos de usuario protegidos por la autorización](xref:security/authorization/secure-data) contiene una aplicación de ejemplo que usa la autorización basada en recursos.

## <a name="use-imperative-authorization"></a>Usar autorización imperativa

La autorización se implementa como un servicio [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) y se registra en la colección de servicios dentro de la `Startup` clase. El servicio está disponible a través de la [inserción de dependencias](xref:fundamentals/dependency-injection) en los controladores o acciones de página.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` tiene dos `AuthorizeAsync` sobrecargas de método: una que acepta el recurso y el nombre de la Directiva y la otra acepta el recurso y una lista de requisitos para evaluar.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

En el ejemplo siguiente, el recurso que se va a proteger se carga en un `Document` objeto personalizado. `AuthorizeAsync`Se invoca una sobrecarga para determinar si el usuario actual tiene permiso para editar el documento proporcionado. Una directiva de autorización "EditPolicy" personalizada se factoriza en la decisión. Consulte [autorización personalizada basada en directivas](xref:security/authorization/policies) para obtener más información sobre la creación de directivas de autorización.

> [!NOTE]
> En los ejemplos de código siguientes se asume que la autenticación se ha ejecutado y se ha establecido la `User` propiedad.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Escribir un controlador basado en recursos

Escribir un controlador para la autorización basada en recursos no es muy diferente que [escribir un controlador de requisitos sin formato](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Cree una clase de requisito personalizada e implemente una clase de controlador de requisitos. Para obtener más información sobre cómo crear una clase de requisito, consulte [requisitos](xref:security/authorization/policies#requirements).

La clase de controlador especifica el requisito y el tipo de recurso. Por ejemplo, un controlador que utiliza un `SameAuthorRequirement` y un `Document` recurso sigue:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

En el ejemplo anterior, Imagine que `SameAuthorRequirement` es un caso especial de una clase más genérica `SpecificAuthorRequirement` . La `SpecificAuthorRequirement` clase (no se muestra) contiene una `Name` propiedad que representa el nombre del autor. La `Name` propiedad se puede establecer en el usuario actual.

Registre el requisito y el controlador en `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>Requisitos operativos

Si va a tomar decisiones basadas en los resultados de las operaciones CRUD (crear, leer, actualizar, eliminar), use la clase auxiliar [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) . Esta clase le permite escribir un único controlador en lugar de una clase individual para cada tipo de operación. Para usarlo, proporcione algunos nombres de operación:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

El controlador se implementa como sigue, mediante un `OperationAuthorizationRequirement` requisito y un `Document` recurso:

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

El controlador anterior valida la operación mediante el recurso, la identidad del usuario y la propiedad del requisito `Name` .

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>Desafío y prohibido con un controlador de recursos operativos

En esta sección se muestra cómo se procesan los resultados de las acciones de desafío y de prohibido y cómo difieren desafío y prohibido.

Para llamar a un controlador de recursos operativo, especifique la operación al invocar `AuthorizeAsync` en el controlador de páginas o en la acción. En el ejemplo siguiente se determina si se permite al usuario autenticado ver el documento proporcionado.

> [!NOTE]
> En los ejemplos de código siguientes se asume que la autenticación se ha ejecutado y se ha establecido la `User` propiedad.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Si la autorización se realiza correctamente, se devuelve la página para ver el documento. Si se produce un error en la autorización pero el usuario está autenticado, al devolver se `ForbidResult` informa a cualquier middleware de autenticación de que se ha producido un error de autorización. `ChallengeResult`Se devuelve un cuando se debe realizar la autenticación. En el caso de los clientes de explorador interactivos, puede ser adecuado redirigir al usuario a una página de inicio de sesión.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Si la autorización se realiza correctamente, se devuelve la vista para el documento. Si se produce un error en la autorización, `ChallengeResult` la devolución informa al middleware de autenticación de que se produjo un error en la autorización y el middleware puede tomar la respuesta adecuada. Una respuesta adecuada podría devolver un código de estado 401 o 403. En el caso de los clientes interactivos del explorador, podría significar redirigir al usuario a una página de inicio de sesión.

::: moniker-end
