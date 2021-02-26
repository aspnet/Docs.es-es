---
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
ms.openlocfilehash: d6c3c1800bd341cc1c21ec6ec80421932dae61df
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552240"
---
## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="b5f4b-101">UserManager y SignInManager</span><span class="sxs-lookup"><span data-stu-id="b5f4b-101">UserManager and SignInManager</span></span>

<span data-ttu-id="b5f4b-102">Establezca el tipo de notificaciones de identificador de usuario cuando una aplicación de servidor requiera:</span><span class="sxs-lookup"><span data-stu-id="b5f4b-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="b5f4b-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> o <xref:Microsoft.AspNetCore.Identity.SignInManager%601> en un punto de conexión de API.</span><span class="sxs-lookup"><span data-stu-id="b5f4b-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="b5f4b-104">Detalles de <xref:Microsoft.AspNetCore.Identity.IdentityUser>, como el nombre de usuario, la dirección de correo electrónico o la hora de finalización del bloqueo.</span><span class="sxs-lookup"><span data-stu-id="b5f4b-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="b5f4b-105">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b5f4b-105">In `Startup.ConfigureServices`:</span></span>

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="b5f4b-106">El elemento `WeatherForecastController` siguiente registra la propiedad <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> cuando se llama al método `Get`:</span><span class="sxs-lookup"><span data-stu-id="b5f4b-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            this.userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
