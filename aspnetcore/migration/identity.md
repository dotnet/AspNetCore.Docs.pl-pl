---
title: Migrowanie uwierzytelniania i Identity ASP.NET Core
author: ardalis
description: Dowiedz się, jak migrować uwierzytelnianie i tożsamość z projektu ASP.NET MVC do projektu MVC ASP.NET Core.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 85674d7ed02f189f78da92bffdf927a3ca21357d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629512"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a><span data-ttu-id="2ff1f-103">Migrowanie uwierzytelniania i Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ff1f-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="2ff1f-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2ff1f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2ff1f-105">W poprzednim artykule [przeprowadzono migrację konfiguracji z projektu ASP.NET MVC do ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="2ff1f-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="2ff1f-106">W tym artykule przeprowadzimy migrację funkcji rejestracji, logowania i zarządzania użytkownikami.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-no-locidentity-and-membership"></a><span data-ttu-id="2ff1f-107">Konfigurowanie Identity i członkostwo</span><span class="sxs-lookup"><span data-stu-id="2ff1f-107">Configure Identity and Membership</span></span>

<span data-ttu-id="2ff1f-108">W ASP.NET MVC funkcje uwierzytelniania i tożsamości są konfigurowane przy użyciu ASP.NET Identity w *Startup.Auth.cs* i \* Identity config.cs\*, które znajdują się w folderze *App_Start* .</span><span class="sxs-lookup"><span data-stu-id="2ff1f-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="2ff1f-109">W ASP.NET Core MVC te funkcje są konfigurowane w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="2ff1f-110">Zainstaluj następujące pakiety NuGet:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-110">Install the the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="2ff1f-111">W programie *Startup.cs*zaktualizuj `Startup.ConfigureServices` metodę, aby używać Entity Framework i Identity usług:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-111">In *Startup.cs*, update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="2ff1f-112">W tym momencie istnieją dwa typy, do których odwołuje się powyższy kod, który nie został jeszcze zmigrowany z projektu ASP.NET MVC: `ApplicationDbContext` i `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="2ff1f-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="2ff1f-113">Utwórz nowy folder *models* w projekcie ASP.NET Core i Dodaj do niego dwie klasy odpowiadające tym typom.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="2ff1f-114">Wersje ASP.NET MVC tych klas można znaleźć w */Models/ Identity models.cs*, ale będziemy używać jednego pliku dla każdej klasy w zmigrowanym projekcie, ponieważ jest to bardziej jasne.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="2ff1f-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="2ff1f-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-116">*ApplicationDbContext.cs*:</span></span>

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

<span data-ttu-id="2ff1f-117">Projekt sieci Web ASP.NET Core MVC Starter nie obejmuje znacznie dostosowywania użytkowników ani `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="2ff1f-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="2ff1f-118">W przypadku migrowania prawdziwej aplikacji należy również migrować wszystkie niestandardowe właściwości i metody użytkownika i klasy aplikacji oraz `DbContext` inne klasy modelu używane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="2ff1f-119">Na przykład jeśli masz `DbContext` `DbSet<Album>` , musisz zmigrować `Album` klasę.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="2ff1f-120">Po wykonaniu tych plików plik *Startup.cs* można kompilować, aktualizując `using` instrukcje:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="2ff1f-121">Nasza aplikacja jest teraz gotowa do obsługi uwierzytelniania i Identity usług.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="2ff1f-122">Wystarczy, że te funkcje są dostępne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="2ff1f-123">Migrowanie logiki rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="2ff1f-123">Migrate registration and login logic</span></span>

<span data-ttu-id="2ff1f-124">Za pomocą Identity usług skonfigurowanych dla aplikacji i dostępu do danych skonfigurowanych przy użyciu Entity Framework i SQL Server, jesteśmy gotowi do dodawania obsługi rejestracji i logowania do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="2ff1f-125">Odwołaj się [wcześniej w procesie migracji](xref:migration/mvc#migrate-the-layout-file) , który dodał odwołanie do *_LoginPartial* w *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="2ff1f-126">Teraz czas na powrót do tego kodu, usunięcie komentarza i dodanie do niezbędnych kontrolerów i widoków do obsługi funkcji logowania.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="2ff1f-127">Usuń komentarz z `@Html.Partial` wiersza w *_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2ff1f-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="2ff1f-128">Teraz Dodaj nowy Razor widok o nazwie *_LoginPartial* do *widoku/folderu udostępnionego* :</span><span class="sxs-lookup"><span data-stu-id="2ff1f-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="2ff1f-129">Zaktualizuj *_LoginPartial. cshtml* przy użyciu następującego kodu (Zastąp całą jego zawartość):</span><span class="sxs-lookup"><span data-stu-id="2ff1f-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="2ff1f-130">W tym momencie powinno być możliwe odświeżenie witryny w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="2ff1f-131">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="2ff1f-131">Summary</span></span>

<span data-ttu-id="2ff1f-132">ASP.NET Core wprowadza zmiany w Identity funkcjach ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="2ff1f-133">W tym artykule pokazano, jak przeprowadzić migrację funkcji zarządzania uwierzytelnianiem i użytkownikami ASP.NET Identity do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ff1f-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
