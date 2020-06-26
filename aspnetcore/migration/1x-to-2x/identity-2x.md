---
title: Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0
author: scottaddie
description: W tym artykule opisano najczęstsze kroki migracji ASP.NET Core 1. x oraz Identity do ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: dacf6fa7191f51f36b9ba65a90746a26f958fc03
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408672"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="b70eb-103">Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="b70eb-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="b70eb-104">Przez [Scott Addie](https://github.com/scottaddie) i [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="b70eb-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="b70eb-105">ASP.NET Core 2,0 ma nowy model uwierzytelniania i [Identity](xref:security/authentication/identity) upraszcza konfigurację przy użyciu usług.</span><span class="sxs-lookup"><span data-stu-id="b70eb-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="b70eb-106">ASP.NET Core 1. x aplikacji korzystających z uwierzytelniania lub Identity można je zaktualizować, aby użyć nowego modelu, jak opisano poniżej.</span><span class="sxs-lookup"><span data-stu-id="b70eb-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="b70eb-107">Aktualizowanie przestrzeni nazw</span><span class="sxs-lookup"><span data-stu-id="b70eb-107">Update namespaces</span></span>

<span data-ttu-id="b70eb-108">W 1. x klasy takie jak `IdentityRole` i `IdentityUser` zostały znalezione w `Microsoft.AspNetCore.Identity.EntityFrameworkCore` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b70eb-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="b70eb-109">W 2,0, <xref:Microsoft.AspNetCore.Identity> przestrzeń nazw stało się nowym domem dla kilku takich klas.</span><span class="sxs-lookup"><span data-stu-id="b70eb-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="b70eb-110">W przypadku domyślnego Identity kodu klasy, których to dotyczy, obejmują `ApplicationUser` i `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="b70eb-111">Dostosuj swoje `using` instrukcje, aby rozwiązać odnośne odwołania.</span><span class="sxs-lookup"><span data-stu-id="b70eb-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="b70eb-112">Uwierzytelnianie i oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="b70eb-112">Authentication Middleware and services</span></span>

<span data-ttu-id="b70eb-113">W projektach 1. x uwierzytelnianie jest konfigurowane za pośrednictwem oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="b70eb-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="b70eb-114">Metoda pośrednicząca jest wywoływana dla każdego schematu uwierzytelniania, który ma być obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="b70eb-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="b70eb-115">Poniższy przykład 1. x konfiguruje uwierzytelnianie w serwisie Facebook Identity w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="b70eb-116">W przypadku projektów 2,0 uwierzytelnianie jest konfigurowane za pośrednictwem usług.</span><span class="sxs-lookup"><span data-stu-id="b70eb-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="b70eb-117">Każdy schemat uwierzytelniania jest rejestrowany w `ConfigureServices` metodzie *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b70eb-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="b70eb-118">`UseIdentity`Metoda jest zastępowana przez `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="b70eb-119">Poniższy przykład 2,0 służy do konfigurowania uwierzytelniania w serwisie Facebook Identity w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="b70eb-120">`UseAuthentication`Metoda dodaje pojedynczy składnik pośredniczący uwierzytelniania, który jest odpowiedzialny za automatyczne uwierzytelnianie i obsługę żądań uwierzytelniania zdalnego.</span><span class="sxs-lookup"><span data-stu-id="b70eb-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="b70eb-121">Zastępuje wszystkie poszczególne składniki pośredniczące pojedynczym, wspólnym składnikiem pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="b70eb-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="b70eb-122">Poniżej przedstawiono 2,0 instrukcje dotyczące migracji dla każdego głównego schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="b70eb-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="b70eb-123">Uwierzytelnianie na podstawie plików cookie</span><span class="sxs-lookup"><span data-stu-id="b70eb-123">Cookie-based authentication</span></span>

<span data-ttu-id="b70eb-124">Wybierz jedną z dwóch opcji poniżej i wprowadź niezbędne zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="b70eb-125">Używanie plików cookie zIdentity</span><span class="sxs-lookup"><span data-stu-id="b70eb-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="b70eb-126">Zamień `UseIdentity` na `UseAuthentication` w `Configure` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="b70eb-127">Wywołaj `AddIdentity` metodę w `ConfigureServices` metodzie, aby dodać usługi uwierzytelniania plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b70eb-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="b70eb-128">Opcjonalnie Wywołaj `ConfigureApplicationCookie` metodę lub `ConfigureExternalCookie` w `ConfigureServices` metodzie, aby dostosować Identity Ustawienia plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b70eb-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="b70eb-129">Używanie plików cookie bezIdentity</span><span class="sxs-lookup"><span data-stu-id="b70eb-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="b70eb-130">Zastąp `UseCookieAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="b70eb-131">Wywołaj `AddAuthentication` `AddCookie` metody i w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="b70eb-132">Uwierzytelnianie okaziciela JWT</span><span class="sxs-lookup"><span data-stu-id="b70eb-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="b70eb-133">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="b70eb-134">Zastąp `UseJwtBearerAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-135">Wywołaj `AddJwtBearer` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="b70eb-136">Ten fragment kodu nie używa Identity , dlatego należy ustawić domyślny schemat, przekazując `JwtBearerDefaults.AuthenticationScheme` do `AddAuthentication` metody.</span><span class="sxs-lookup"><span data-stu-id="b70eb-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="b70eb-137">Uwierzytelnianie OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="b70eb-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="b70eb-138">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="b70eb-139">Zastąp `UseOpenIdConnectAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-140">Wywołaj `AddOpenIdConnect` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="b70eb-141">Zastąp `PostLogoutRedirectUri` Właściwość w `OpenIdConnectOptions` akcji `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="b70eb-142">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="b70eb-142">Facebook authentication</span></span>

<span data-ttu-id="b70eb-143">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="b70eb-144">Zastąp `UseFacebookAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-145">Wywołaj `AddFacebook` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="b70eb-146">Uwierzytelnianie przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="b70eb-146">Google authentication</span></span>

<span data-ttu-id="b70eb-147">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="b70eb-148">Zastąp `UseGoogleAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-149">Wywołaj `AddGoogle` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="b70eb-150">Uwierzytelnianie za pomocą konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="b70eb-150">Microsoft Account authentication</span></span>

<span data-ttu-id="b70eb-151">Aby uzyskać więcej informacji na temat uwierzytelniania konto Microsoft, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/14455)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="b70eb-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="b70eb-152">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="b70eb-153">Zastąp `UseMicrosoftAccountAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-154">Wywołaj `AddMicrosoftAccount` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="b70eb-155">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="b70eb-155">Twitter authentication</span></span>

<span data-ttu-id="b70eb-156">Wprowadź następujące zmiany w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="b70eb-157">Zastąp `UseTwitterAuthentication` wywołanie metody w `Configure` metodzie `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b70eb-158">Wywołaj `AddTwitter` metodę w `ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="b70eb-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="b70eb-159">Ustawianie domyślnych schematów uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b70eb-159">Setting default authentication schemes</span></span>

<span data-ttu-id="b70eb-160">W 1. x, `AutomaticAuthenticate` właściwości i `AutomaticChallenge` klasy bazowej [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) zostały przeznaczone do ustawienia w jednym schemacie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="b70eb-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="b70eb-161">Nie było dobrym sposobem wymuszenia tego.</span><span class="sxs-lookup"><span data-stu-id="b70eb-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="b70eb-162">W 2,0 te dwie właściwości zostały usunięte jako właściwości w poszczególnych `AuthenticationOptions` wystąpieniach.</span><span class="sxs-lookup"><span data-stu-id="b70eb-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="b70eb-163">Można je skonfigurować w `AddAuthentication` wywołaniu metody w `ConfigureServices` metodzie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="b70eb-164">W poprzednim fragmencie kodu domyślny schemat jest ustawiany na `CookieAuthenticationDefaults.AuthenticationScheme` ("pliki cookie").</span><span class="sxs-lookup"><span data-stu-id="b70eb-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="b70eb-165">Alternatywnie można użyć przeciążonej wersji `AddAuthentication` metody do ustawienia więcej niż jednej właściwości.</span><span class="sxs-lookup"><span data-stu-id="b70eb-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="b70eb-166">W poniższym przykładzie przeciążonej metody domyślny schemat jest ustawiany na `CookieAuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="b70eb-167">Schemat uwierzytelniania można także określić w ramach poszczególnych `[Authorize]` atrybutów lub zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="b70eb-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="b70eb-168">Zdefiniuj domyślny schemat w 2,0, jeśli spełniony jest jeden z następujących warunków:</span><span class="sxs-lookup"><span data-stu-id="b70eb-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="b70eb-169">Chcesz, aby użytkownik był zalogowany automatycznie</span><span class="sxs-lookup"><span data-stu-id="b70eb-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="b70eb-170">Używasz `[Authorize]` zasad atrybutu lub autoryzacji bez określania schematów</span><span class="sxs-lookup"><span data-stu-id="b70eb-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="b70eb-171">Wyjątkiem od tej reguły jest `AddIdentity` Metoda.</span><span class="sxs-lookup"><span data-stu-id="b70eb-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="b70eb-172">Ta metoda umożliwia dodanie plików cookie i ustawienie domyślnych schematów uwierzytelniania i wyzwania do pliku cookie aplikacji `IdentityConstants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="b70eb-173">Ponadto ustawia domyślny schemat logowania na zewnętrzny plik cookie `IdentityConstants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="b70eb-174">Korzystanie z rozszerzeń kontekstu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b70eb-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="b70eb-175">`IAuthenticationManager`Interfejs jest głównym punktem wejścia do systemu uwierzytelniania 1. x.</span><span class="sxs-lookup"><span data-stu-id="b70eb-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="b70eb-176">Został on zastąpiony nowym zestawem `HttpContext` metod rozszerzających w `Microsoft.AspNetCore.Authentication` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="b70eb-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="b70eb-177">Na przykład projekty 1. x odwołują się do `Authentication` Właściwości:</span><span class="sxs-lookup"><span data-stu-id="b70eb-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b70eb-178">W projektach 2,0, zaimportuj `Microsoft.AspNetCore.Authentication` przestrzeń nazw i Usuń `Authentication` odwołania do właściwości:</span><span class="sxs-lookup"><span data-stu-id="b70eb-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="b70eb-179">Uwierzytelnianie systemu Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="b70eb-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="b70eb-180">Istnieją dwie różne warianty uwierzytelniania systemu Windows:</span><span class="sxs-lookup"><span data-stu-id="b70eb-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="b70eb-181">Host zezwala tylko uwierzytelnionym użytkownikom.</span><span class="sxs-lookup"><span data-stu-id="b70eb-181">The host only allows authenticated users.</span></span> <span data-ttu-id="b70eb-182">Ta zmiana nie ma wpływ na 2,0 zmian.</span><span class="sxs-lookup"><span data-stu-id="b70eb-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="b70eb-183">Host umożliwia zarówno anonimowych, jak i uwierzytelnionych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="b70eb-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="b70eb-184">Ta zmiana ma wpływ na zmiany 2,0.</span><span class="sxs-lookup"><span data-stu-id="b70eb-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="b70eb-185">Na przykład aplikacja powinna zezwalać na anonimowych użytkowników w warstwie [usług IIS](xref:host-and-deploy/iis/index) lub [HTTP.sys](xref:fundamentals/servers/httpsys) , ale autoryzuje użytkowników na poziomie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="b70eb-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="b70eb-186">W tym scenariuszu Ustaw domyślny schemat w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="b70eb-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="b70eb-187">Dla elementu [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)Ustaw domyślny schemat na `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="b70eb-188">Dla elementu [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)Ustaw domyślny schemat na `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="b70eb-189">Nie można ustawić domyślnego schematu, aby zapobiec działaniu żądania Autoryzuj (wyzwanie) z powodu następującego wyjątku:</span><span class="sxs-lookup"><span data-stu-id="b70eb-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="b70eb-190">`System.InvalidOperationException`: Nie określono authenticationScheme i nie znaleziono DefaultChallengeScheme.</span><span class="sxs-lookup"><span data-stu-id="b70eb-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="b70eb-191">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="b70eb-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="b70eb-192">Wystąpienia IdentityCookieOptions</span><span class="sxs-lookup"><span data-stu-id="b70eb-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="b70eb-193">Efektem ubocznym zmian 2,0 jest przełączenie do użycia nazwanych opcji zamiast wystąpień opcji plików cookie.</span><span class="sxs-lookup"><span data-stu-id="b70eb-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="b70eb-194">Możliwość dostosowywania Identity nazw schematu plików cookie jest usuwana.</span><span class="sxs-lookup"><span data-stu-id="b70eb-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="b70eb-195">Na przykład projekty 1. x wykorzystują [iniekcję konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) do przekazania `IdentityCookieOptions` parametru do *AccountController.cs* i *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="b70eb-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="b70eb-196">Do schematu uwierzytelniania zewnętrznego pliku cookie jest uzyskiwany dostęp z podanego wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="b70eb-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="b70eb-197">Wspomniane wyżej iniekcja konstruktora nie jest konieczna w projektach 2,0 i `_externalCookieScheme` można usunąć pole:</span><span class="sxs-lookup"><span data-stu-id="b70eb-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="b70eb-198">1. x projekty używały `_externalCookieScheme` pola w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="b70eb-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b70eb-199">W projektach 2,0 Zastąp poprzedni kod poniższym kodem.</span><span class="sxs-lookup"><span data-stu-id="b70eb-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="b70eb-200">`IdentityConstants.ExternalScheme`Stała może być używana bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="b70eb-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b70eb-201">Usuń nowo dodane `SignOutAsync` wywołanie, importując następującą przestrzeń nazw:</span><span class="sxs-lookup"><span data-stu-id="b70eb-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="b70eb-202">Dodawanie właściwości nawigacji IdentityUser POCO</span><span class="sxs-lookup"><span data-stu-id="b70eb-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="b70eb-203">Wszystkie podstawowe właściwości nawigacji Entity Framework (EF) podstawowego `IdentityUser` poco (stary obiekt CLR) zostały usunięte.</span><span class="sxs-lookup"><span data-stu-id="b70eb-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="b70eb-204">Jeśli projekt 1. x użył tych właściwości, ręcznie dodaj je z powrotem do projektu 2,0:</span><span class="sxs-lookup"><span data-stu-id="b70eb-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="b70eb-205">Aby zapobiec duplikowaniu kluczy obcych podczas uruchamiania EF Core migracji, Dodaj następujące elementy do `IdentityDbContext` `OnModelCreating` metody klasy (po `base.OnModelCreating();` wywołaniu):</span><span class="sxs-lookup"><span data-stu-id="b70eb-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="b70eb-206">Zastąp GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="b70eb-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="b70eb-207">Metoda synchroniczna `GetExternalAuthenticationSchemes` została usunięta na korzyść asynchronicznej wersji.</span><span class="sxs-lookup"><span data-stu-id="b70eb-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="b70eb-208">1. x projekty mają następujący kod w obszarze *controllers/ManageController. cs*:</span><span class="sxs-lookup"><span data-stu-id="b70eb-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="b70eb-209">Ta metoda pojawia się w obszarze *widoki/Account/Login. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b70eb-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="b70eb-210">W projektach 2,0 użyj <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="b70eb-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="b70eb-211">Zmiana w *ManageController.cs* przypomina następujący kod:</span><span class="sxs-lookup"><span data-stu-id="b70eb-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="b70eb-212">W polu *login. cshtml*właściwość, do której `AuthenticationScheme` uzyskuje się dostęp w `foreach` pętli, zmieni się na `Name` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="b70eb-213">Zmiana właściwości ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="b70eb-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="b70eb-214">`ManageLoginsViewModel`Obiekt jest używany w `ManageLogins` akcji *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="b70eb-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="b70eb-215">W projektach 1. x `OtherLogins` Typ zwracany właściwości obiektu to `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="b70eb-216">Ten typ zwracany wymaga importu `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="b70eb-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="b70eb-217">W projektach 2,0 typ zwracany zmieni się na `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="b70eb-218">Ten nowy typ zwracany wymaga zastąpienia importu importem `Microsoft.AspNetCore.Http.Authentication` `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="b70eb-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="b70eb-219">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b70eb-219">Additional resources</span></span>

<span data-ttu-id="b70eb-220">Aby uzyskać więcej informacji, zobacz [Omówienie problemu z uwierzytelnianiem 2,0](https://github.com/aspnet/Security/issues/1338) w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="b70eb-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
