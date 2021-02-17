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
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552792"
---
## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="73806-101">Usermanager i SignInManager</span><span class="sxs-lookup"><span data-stu-id="73806-101">UserManager and SignInManager</span></span>

<span data-ttu-id="73806-102">Ustaw typ żądania identyfikatora użytkownika, gdy aplikacja serwera wymaga:</span><span class="sxs-lookup"><span data-stu-id="73806-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="73806-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> lub <xref:Microsoft.AspNetCore.Identity.SignInManager%601> w punkcie końcowym interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="73806-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="73806-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> Szczegóły, takie jak nazwa użytkownika, adres e-mail lub czas zakończenia blokady.</span><span class="sxs-lookup"><span data-stu-id="73806-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="73806-105">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="73806-105">In `Startup.ConfigureServices`:</span></span>

```csharp
using System.Security.Claims;

...

services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="73806-106">Następujące `WeatherForecastController` dzienniki są <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> wywoływane, gdy `Get` wywoływana jest metoda:</span><span class="sxs-lookup"><span data-stu-id="73806-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

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
