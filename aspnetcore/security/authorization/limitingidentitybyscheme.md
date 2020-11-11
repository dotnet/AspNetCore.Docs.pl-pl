---
title: Autoryzuj z określonym schematem w ASP.NET Core
author: rick-anderson
description: W tym artykule wyjaśniono, jak ograniczyć tożsamość do określonego schematu podczas pracy z wieloma metodami uwierzytelniania.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: a5f2dff7b0e0d4f209ba445b2efb6fb261cbaab1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464019"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="2e34e-103">Autoryzuj z określonym schematem w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e34e-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="2e34e-104">W niektórych scenariuszach, takich jak aplikacje jednostronicowe (aplikacji jednostronicowych), często używane są wiele metod uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="2e34e-105">Na przykład aplikacja może używać :::no-loc(cookie)::: uwierzytelniania opartego na usłudze do logowania i uwierzytelniania JWT dla żądań języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2e34e-105">For example, the app may use :::no-loc(cookie):::-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="2e34e-106">W niektórych przypadkach aplikacja może mieć wiele wystąpień programu obsługi uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="2e34e-107">Na przykład dwa :::no-loc(cookie)::: programy obsługi, w których jeden zawiera podstawową tożsamość, a jedna jest tworzona podczas wyzwalania uwierzytelniania wieloskładnikowego (MFA).</span><span class="sxs-lookup"><span data-stu-id="2e34e-107">For example, two :::no-loc(cookie)::: handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="2e34e-108">Uwierzytelnianie wieloskładnikowe może być wyzwalane, ponieważ użytkownik zażądał operacji wymagającej dodatkowych zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="2e34e-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="2e34e-109">Aby uzyskać więcej informacji na temat wymuszania usługi MFA, gdy użytkownik zażąda zasobu wymagającego uwierzytelniania wieloskładnikowego, zobacz [sekcję dotyczącą ochrony za pomocą](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)usługi GitHub i usługi MFA</span><span class="sxs-lookup"><span data-stu-id="2e34e-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="2e34e-110">Schemat uwierzytelniania ma nazwę, gdy usługa uwierzytelniania jest konfigurowana podczas uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="2e34e-111">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="2e34e-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .Add:::no-loc(Cookie):::(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="2e34e-112">W poprzednim kodzie dodano dwa programy obsługi uwierzytelniania: jeden dla :::no-loc(cookie)::: s i jeden dla okaziciela.</span><span class="sxs-lookup"><span data-stu-id="2e34e-112">In the preceding code, two authentication handlers have been added: one for :::no-loc(cookie):::s and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="2e34e-113">Określenie schematu domyślnego powoduje, że `HttpContext.User` Właściwość jest ustawiana na tę tożsamość.</span><span class="sxs-lookup"><span data-stu-id="2e34e-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="2e34e-114">Jeśli takie zachowanie nie jest wymagane, należy je wyłączyć, wywołując formularz bez parametrów `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="2e34e-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="2e34e-115">Wybieranie schematu z atrybutem Autoryzuj</span><span class="sxs-lookup"><span data-stu-id="2e34e-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="2e34e-116">W punkcie autoryzacji Aplikacja wskazuje program obsługi, który ma być używany.</span><span class="sxs-lookup"><span data-stu-id="2e34e-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="2e34e-117">Wybierz program obsługi, za pomocą którego aplikacja będzie autoryzować, przekazując rozdzieloną przecinkami listę schematów uwierzytelniania do `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="2e34e-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="2e34e-118">Ten `[Authorize]` atrybut określa schemat lub schematy uwierzytelniania, które mają być używane niezależnie od tego, czy skonfigurowano wartość domyślną.</span><span class="sxs-lookup"><span data-stu-id="2e34e-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="2e34e-119">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="2e34e-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="2e34e-120">W poprzednim przykładzie zarówno program, :::no-loc(cookie)::: jak i programy obsługi oraz mogą tworzyć i dołączać tożsamość bieżącego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2e34e-120">In the preceding example, both the :::no-loc(cookie)::: and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="2e34e-121">Określając tylko jeden schemat, zostanie uruchomiony odpowiedni program obsługi.</span><span class="sxs-lookup"><span data-stu-id="2e34e-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="2e34e-122">W poprzednim kodzie jest tylko procedura obsługi ze schematem "Bearer".</span><span class="sxs-lookup"><span data-stu-id="2e34e-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="2e34e-123">Wszystkie :::no-loc(cookie)::: tożsamości oparte na usłudze są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="2e34e-123">Any :::no-loc(cookie):::-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="2e34e-124">Wybieranie schematu z zasadami</span><span class="sxs-lookup"><span data-stu-id="2e34e-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="2e34e-125">Jeśli wolisz określić żądane schematy w [zasadach](xref:security/authorization/policies), możesz ustawić `AuthenticationSchemes` kolekcję podczas dodawania zasad:</span><span class="sxs-lookup"><span data-stu-id="2e34e-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="2e34e-126">W poprzednim przykładzie zasada "Over18" działa tylko w odniesieniu do tożsamości utworzonej przez procedurę obsługi "Bearer".</span><span class="sxs-lookup"><span data-stu-id="2e34e-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="2e34e-127">Użyj zasad, ustawiając `[Authorize]` `Policy` właściwość atrybutu:</span><span class="sxs-lookup"><span data-stu-id="2e34e-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="2e34e-128">Używanie wielu schematów uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2e34e-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="2e34e-129">Niektóre aplikacje mogą wymagać obsługi wielu typów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="2e34e-130">Na przykład aplikacja może uwierzytelniać użytkowników z Azure Active Directory i z bazy danych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="2e34e-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="2e34e-131">Innym przykładem jest aplikacja, która uwierzytelnia użytkowników zarówno z Active Directory Federation Services, jak i Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="2e34e-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="2e34e-132">W takim przypadku aplikacja powinna akceptować token okaziciela JWT z kilku wystawców.</span><span class="sxs-lookup"><span data-stu-id="2e34e-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="2e34e-133">Dodaj wszystkie schematy uwierzytelniania, które chcesz zaakceptować.</span><span class="sxs-lookup"><span data-stu-id="2e34e-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="2e34e-134">Na przykład poniższy kod w programie `Startup.ConfigureServices` dodaje dwa systemy uwierzytelniania okaziciela JWT z różnymi wystawcami:</span><span class="sxs-lookup"><span data-stu-id="2e34e-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="2e34e-135">Zarejestrowano tylko jedno uwierzytelnianie okaziciela JWT z domyślnym schematem uwierzytelniania `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="2e34e-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="2e34e-136">Dodatkowe uwierzytelnianie musi być zarejestrowane przy użyciu unikatowego schematu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="2e34e-137">Następnym krokiem jest zaktualizowanie domyślnych zasad autoryzacji w celu zaakceptowania obu schematów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="2e34e-138">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="2e34e-138">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="2e34e-139">Ponieważ domyślne zasady autoryzacji są zastępowane, można użyć `[Authorize]` atrybutu w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="2e34e-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="2e34e-140">Kontroler akceptuje żądania z tokenem JWT wystawionym przez pierwszego lub drugiego wystawcy.</span><span class="sxs-lookup"><span data-stu-id="2e34e-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="2e34e-141">[Ten problem](https://github.com/dotnet/aspnetcore/issues/26002) z usługą GitHub można znaleźć w artykule dotyczącym wielu schematów uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="2e34e-141">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
