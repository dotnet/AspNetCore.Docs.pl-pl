---
title: Migrowanie uwierzytelniania i Identity ASP.NET Core
author: ardalis
description: Dowiedz się, jak migrować uwierzytelnianie i tożsamość z projektu ASP.NET MVC do projektu MVC ASP.NET Core.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060654"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>Migrowanie uwierzytelniania i Identity ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

W poprzednim artykule [przeprowadzono migrację konfiguracji z projektu ASP.NET MVC do ASP.NET Core MVC](xref:migration/configuration). W tym artykule przeprowadzimy migrację funkcji rejestracji, logowania i zarządzania użytkownikami.

## <a name="configure-no-locidentity-and-membership"></a>Konfigurowanie Identity i członkostwo

W ASP.NET MVC funkcje uwierzytelniania i tożsamości są konfigurowane przy użyciu ASP.NET Identity w *Startup.Auth.cs* i *Identity config.cs* , które znajdują się w folderze *App_Start* . W ASP.NET Core MVC te funkcje są konfigurowane w *Startup.cs* .

Zainstaluj następujące pakiety NuGet:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

W programie *Startup.cs* zaktualizuj `Startup.ConfigureServices` metodę, aby używać Entity Framework i Identity usług:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

W tym momencie istnieją dwa typy, do których odwołuje się powyższy kod, który nie został jeszcze zmigrowany z projektu ASP.NET MVC: `ApplicationDbContext` i `ApplicationUser` . Utwórz nowy folder *models* w projekcie ASP.NET Core i Dodaj do niego dwie klasy odpowiadające tym typom. Wersje ASP.NET MVC tych klas można znaleźć w */Models/ Identity models.cs* , ale będziemy używać jednego pliku dla każdej klasy w zmigrowanym projekcie, ponieważ jest to bardziej jasne.

*ApplicationUser.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Projekt sieci Web ASP.NET Core MVC Starter nie obejmuje znacznie dostosowywania użytkowników ani `ApplicationDbContext` . W przypadku migrowania prawdziwej aplikacji należy również migrować wszystkie niestandardowe właściwości i metody użytkownika i klasy aplikacji oraz `DbContext` inne klasy modelu używane przez aplikację. Na przykład jeśli masz `DbContext` `DbSet<Album>` , musisz zmigrować `Album` klasę.

Po wykonaniu tych plików plik *Startup.cs* można kompilować, aktualizując `using` instrukcje:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Nasza aplikacja jest teraz gotowa do obsługi uwierzytelniania i Identity usług. Wystarczy, że te funkcje są dostępne dla użytkowników.

## <a name="migrate-registration-and-login-logic"></a>Migrowanie logiki rejestracji i logowania

Za pomocą Identity usług skonfigurowanych dla aplikacji i dostępu do danych skonfigurowanych przy użyciu Entity Framework i SQL Server, jesteśmy gotowi do dodawania obsługi rejestracji i logowania do aplikacji. Odwołaj się [wcześniej w procesie migracji](xref:migration/mvc#migrate-the-layout-file) , który dodał odwołanie do *_LoginPartial* w *_Layout. cshtml* . Teraz czas na powrót do tego kodu, usunięcie komentarza i dodanie do niezbędnych kontrolerów i widoków do obsługi funkcji logowania.

Usuń komentarz z `@Html.Partial` wiersza w *_Layout. cshtml* :

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Teraz Dodaj nowy Razor widok o nazwie *_LoginPartial* do *widoku/folderu udostępnionego* :

Zaktualizuj *_LoginPartial. cshtml* przy użyciu następującego kodu (Zastąp całą jego zawartość):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

W tym momencie powinno być możliwe odświeżenie witryny w przeglądarce.

## <a name="summary"></a>Podsumowanie

ASP.NET Core wprowadza zmiany w Identity funkcjach ASP.NET. W tym artykule pokazano, jak przeprowadzić migrację funkcji zarządzania uwierzytelnianiem i użytkownikami ASP.NET Identity do ASP.NET Core.
