---
title: Parte 10. Examen de los métodos Details y Delete de una aplicación de ASP.NET Core
author: rick-anderson
description: Parte 10 de la serie de tutoriales sobre ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 9ceb6c3c8a6622d6e203641c2ce97a483d3d4325
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050750"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="e0436-103">Parte 10. Examen de los métodos Details y Delete de una aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0436-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="e0436-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0436-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0436-105">Abra el controlador Movie y examine el método `Details`:</span><span class="sxs-lookup"><span data-stu-id="e0436-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="e0436-106">El motor de scaffolding de MVC que creó este método de acción agrega un comentario en el que se muestra una solicitud HTTP que invoca el método.</span><span class="sxs-lookup"><span data-stu-id="e0436-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="e0436-107">En este caso se trata de una solicitud GET con tres segmentos de dirección URL, el controlador `Movies`, el método `Details` y un valor `id`.</span><span class="sxs-lookup"><span data-stu-id="e0436-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="e0436-108">Recuerde que estos segmentos se definen en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="e0436-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="e0436-109">EF facilita el proceso de búsqueda de datos mediante el método `FirstOrDefaultAsync`.</span><span class="sxs-lookup"><span data-stu-id="e0436-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="e0436-110">Una característica de seguridad importante integrada en el método es que el código comprueba que el método de búsqueda haya encontrado una película antes de intentar hacer nada con ella.</span><span class="sxs-lookup"><span data-stu-id="e0436-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="e0436-111">Por ejemplo, un pirata informático podría introducir errores en el sitio cambiando la dirección URL creada por los vínculos de `http://localhost:{PORT}/Movies/Details/1` a algo parecido a `http://localhost:{PORT}/Movies/Details/12345` (o algún otro valor que no represente una película real).</span><span class="sxs-lookup"><span data-stu-id="e0436-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="e0436-112">Si no comprobara una película null, la aplicación generaría una excepción.</span><span class="sxs-lookup"><span data-stu-id="e0436-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="e0436-113">Examine los métodos `Delete` y `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="e0436-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="e0436-114">Tenga en cuenta que el método `HTTP GET Delete` no elimina la película especificada, sino que devuelve una vista de la película donde puede enviar (HttpPost) la eliminación.</span><span class="sxs-lookup"><span data-stu-id="e0436-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="e0436-115">La acción de efectuar una operación de eliminación en respuesta a una solicitud GET (o con este propósito efectuar una operación de edición, creación o cualquier otra operación que modifique los datos) genera una vulnerabilidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0436-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="e0436-116">El método `[HttpPost]` que elimina los datos se denomina `DeleteConfirmed` para proporcionar al método HTTP POST una firma o nombre únicos.</span><span class="sxs-lookup"><span data-stu-id="e0436-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="e0436-117">Las dos firmas de método se muestran a continuación:</span><span class="sxs-lookup"><span data-stu-id="e0436-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="e0436-118">Common Language Runtime (CLR) requiere métodos sobrecargados para disponer de una firma de parámetro única (mismo nombre de método, pero lista de parámetros diferente).</span><span class="sxs-lookup"><span data-stu-id="e0436-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="e0436-119">En cambio, aquí necesita dos métodos `Delete` (uno para GET y otro para POST) que tienen la misma firma de parámetro</span><span class="sxs-lookup"><span data-stu-id="e0436-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="e0436-120">(ambos deben aceptar un número entero como parámetro).</span><span class="sxs-lookup"><span data-stu-id="e0436-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="e0436-121">Hay dos enfoques para este problema. Uno consiste en proporcionar nombres diferentes a los métodos,</span><span class="sxs-lookup"><span data-stu-id="e0436-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="e0436-122">que es lo que hizo el mecanismo de scaffolding en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e0436-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="e0436-123">Pero esto implica un pequeño problema: ASP.NET asigna los segmentos de una dirección URL a los métodos de acción por nombre, de modo que si cambia el nombre de un método, el enrutamiento seguramente no podrá encontrar dicho método.</span><span class="sxs-lookup"><span data-stu-id="e0436-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="e0436-124">La solución es la que ve en el ejemplo, que consiste en agregar el atributo `ActionName("Delete")` al método `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="e0436-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="e0436-125">Ese atributo efectúa la asignación para el sistema de enrutamiento para que una dirección URL que incluya /Delete/ para una solicitud POST busque el método `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="e0436-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="e0436-126">Otra solución alternativa común para los métodos que tienen nombres y firmas idénticos consiste en cambiar la firma del método POST artificialmente para incluir un parámetro adicional (sin usar).</span><span class="sxs-lookup"><span data-stu-id="e0436-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="e0436-127">Es lo que hicimos en una publicación anterior, cuando agregamos el parámetro `notUsed`.</span><span class="sxs-lookup"><span data-stu-id="e0436-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="e0436-128">Podría hacer lo mismo aquí para el método `[HttpPost] Delete`:</span><span class="sxs-lookup"><span data-stu-id="e0436-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="e0436-129">Publicar en Azure</span><span class="sxs-lookup"><span data-stu-id="e0436-129">Publish to Azure</span></span>

<span data-ttu-id="e0436-130">Para obtener información sobre la implementación en Azure, consulte [Tutorial: Compilación de una aplicación ASP.NET Core y SQL Database en Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="e0436-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e0436-131">Anterior</span><span class="sxs-lookup"><span data-stu-id="e0436-131">Previous</span></span>](validation.md)
