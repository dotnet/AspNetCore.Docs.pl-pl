## <a name="usermanager-and-signinmanager"></a><span data-ttu-id="7a73f-101">UserManager i SignInManager</span><span class="sxs-lookup"><span data-stu-id="7a73f-101">UserManager and SignInManager</span></span>

<span data-ttu-id="7a73f-102">Ustaw typ oświadczenia identyfikatora użytkownika, gdy aplikacja Serwer wymaga:</span><span class="sxs-lookup"><span data-stu-id="7a73f-102">Set the user identifier claim type when a Server app requires:</span></span>

* <span data-ttu-id="7a73f-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601>lub <xref:Microsoft.AspNetCore.Identity.SignInManager%601> w punkcie końcowym interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7a73f-103"><xref:Microsoft.AspNetCore.Identity.UserManager%601> or <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in an API endpoint.</span></span>
* <span data-ttu-id="7a73f-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser>szczegóły, takie jak imię i nazwisko użytkownika, adres e-mail lub czas zakończenia blokady.</span><span class="sxs-lookup"><span data-stu-id="7a73f-104"><xref:Microsoft.AspNetCore.Identity.IdentityUser> details, such as the user's name, email address, or lockout end time.</span></span>

<span data-ttu-id="7a73f-105">W pliku `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a73f-105">In `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

<span data-ttu-id="7a73f-106">Następujące `WeatherForecastController` <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> dzienniki, gdy `Get` metoda jest wywoływana:</span><span class="sxs-lookup"><span data-stu-id="7a73f-106">The following `WeatherForecastController` logs the <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> when the `Get` method is called:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using BlazorAppIdentityServer.Server.Models;
using BlazorAppIdentityServer.Shared;

namespace BlazorAppIdentityServer.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

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
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

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
