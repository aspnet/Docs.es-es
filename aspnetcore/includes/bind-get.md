> [!WARNING]
> <span data-ttu-id="d96fd-101">Por motivos de seguridad, debe participar en el enlace de datos de solicitud `GET` con las propiedades del modelo de página.</span><span class="sxs-lookup"><span data-stu-id="d96fd-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="d96fd-102">Compruebe las entradas de los usuarios antes de asignarlas a las propiedades.</span><span class="sxs-lookup"><span data-stu-id="d96fd-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="d96fd-103">Si participa en el enlace de `GET`, le puede ser útil al trabajar con escenarios que dependan de cadenas de consultas o valores de rutas.</span><span class="sxs-lookup"><span data-stu-id="d96fd-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="d96fd-104">Para enlazar una propiedad en solicitudes `GET`, establezca la propiedad `SupportsGet` del atributo [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) en `true`:</span><span class="sxs-lookup"><span data-stu-id="d96fd-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="d96fd-105">Para obtener más información, consulte [Reunión de la comunidad de ASP.NET: debate sobre enlazar en GET (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="d96fd-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
