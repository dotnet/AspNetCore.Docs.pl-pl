---
title: Niestandardowi dostawcy zasad autoryzacji w ASP.NET Core
author: mjrousos
description: Dowiedz się, jak używać niestandardowych IAuthorizationPolicyProvider w aplikacji ASP.NET Core do dynamicznego generowania zasad autoryzacji.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053140"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="1f91a-103">Niestandardowi dostawcy zasad autoryzacji korzystający z usługi IAuthorizationPolicyProvider w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f91a-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="1f91a-104">Według [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="1f91a-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="1f91a-105">Zwykle podczas korzystania z [autoryzacji opartej na zasadach](xref:security/authorization/policies)zasady są rejestrowane przez wywołanie `AuthorizationOptions.AddPolicy` w ramach konfiguracji usługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="1f91a-106">W niektórych scenariuszach może nie być możliwe (lub pożądane) rejestrowanie wszystkich zasad autoryzacji w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="1f91a-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="1f91a-107">W takich przypadkach można [użyć niestandardowych `IAuthorizationPolicyProvider` ](#ci) do kontrolowania sposobu dostarczania zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="1f91a-108">Przykłady scenariuszy, w których może być przydatne niestandardowe [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) :</span><span class="sxs-lookup"><span data-stu-id="1f91a-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="1f91a-109">Korzystanie z usługi zewnętrznej w celu zapewnienia oceny zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="1f91a-110">Użycie dużego zakresu zasad (na przykład w przypadku różnych numerów pomieszczeń lub wieku), dlatego nie ma sensu dodania poszczególnych zasad autoryzacji do `AuthorizationOptions.AddPolicy` wywołania.</span><span class="sxs-lookup"><span data-stu-id="1f91a-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="1f91a-111">Tworzenie zasad w czasie wykonywania na podstawie informacji w zewnętrznym źródle danych (np. bazy danych) lub Określanie wymagań dotyczących autoryzacji w sposób dynamiczny przez inny mechanizm.</span><span class="sxs-lookup"><span data-stu-id="1f91a-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="1f91a-112">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) z [repozytorium GitHub AspNetCore](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="1f91a-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="1f91a-113">Pobierz plik ZIP repozytorium dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="1f91a-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="1f91a-114">Rozpakuj plik.</span><span class="sxs-lookup"><span data-stu-id="1f91a-114">Unzip the file.</span></span> <span data-ttu-id="1f91a-115">Przejdź do folderu *src/Security/Samples/CustomPolicyProvider* Project.</span><span class="sxs-lookup"><span data-stu-id="1f91a-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="1f91a-116">Dostosuj pobieranie zasad</span><span class="sxs-lookup"><span data-stu-id="1f91a-116">Customize policy retrieval</span></span>

<span data-ttu-id="1f91a-117">Aplikacje ASP.NET Core korzystają z implementacji `IAuthorizationPolicyProvider` interfejsu w celu pobierania zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="1f91a-118">Domyślnie [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) jest zarejestrowany i używany.</span><span class="sxs-lookup"><span data-stu-id="1f91a-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="1f91a-119">`DefaultAuthorizationPolicyProvider` zwraca zasady z `AuthorizationOptions` podanego w `IServiceCollection.AddAuthorization` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="1f91a-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="1f91a-120">Dostosuj to zachowanie, rejestrując inną `IAuthorizationPolicyProvider` implementację w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="1f91a-121">`IAuthorizationPolicyProvider`Interfejs zawiera trzy interfejsy API:</span><span class="sxs-lookup"><span data-stu-id="1f91a-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="1f91a-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) zwraca zasady autoryzacji dla danej nazwy.</span><span class="sxs-lookup"><span data-stu-id="1f91a-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="1f91a-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) zwraca domyślne zasady autoryzacji (zasady używane dla `[Authorize]` atrybutów bez określonych zasad).</span><span class="sxs-lookup"><span data-stu-id="1f91a-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="1f91a-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) zwraca rezerwowe zasady autoryzacji (zasady używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad).</span><span class="sxs-lookup"><span data-stu-id="1f91a-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="1f91a-125">Implementując te interfejsy API, można dostosować sposób, w jaki są udostępniane zasady autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="1f91a-126">Przykładowy atrybut autoryzacji sparametryzowanej</span><span class="sxs-lookup"><span data-stu-id="1f91a-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="1f91a-127">Jednym z scenariuszy, gdzie `IAuthorizationPolicyProvider` jest przydatna, jest włączenie atrybutów niestandardowych, `[Authorize]` których wymagania zależą od parametru.</span><span class="sxs-lookup"><span data-stu-id="1f91a-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="1f91a-128">Na przykład w dokumentacji dotyczącej [autoryzacji opartej na zasadach](xref:security/authorization/policies) , jako przykład użyto zasad opartych na wieku ("AtLeast21").</span><span class="sxs-lookup"><span data-stu-id="1f91a-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="1f91a-129">Jeśli różne akcje kontrolera w aplikacji powinny być dostępne dla użytkowników z *różnych* okresów obowiązywania, może być przydatne w wielu różnych zasadach opartych na wieku.</span><span class="sxs-lookup"><span data-stu-id="1f91a-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="1f91a-130">Zamiast zarejestrowania wszystkich zasad opartych na wieku, do których aplikacja będzie potrzebna `AuthorizationOptions` , możesz dynamicznie generować zasady za pomocą niestandardowej `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="1f91a-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="1f91a-131">Aby ułatwić korzystanie z zasad, można dodawać adnotacje do akcji przy użyciu niestandardowego atrybutu autoryzacji, takiego jak `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="1f91a-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="1f91a-132">Niestandardowe atrybuty autoryzacji</span><span class="sxs-lookup"><span data-stu-id="1f91a-132">Custom Authorization attributes</span></span>

<span data-ttu-id="1f91a-133">Zasady autoryzacji są identyfikowane przez ich nazwy.</span><span class="sxs-lookup"><span data-stu-id="1f91a-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="1f91a-134">Niestandardowe `MinimumAgeAuthorizeAttribute` opisane wcześniej muszą mapować argumenty na ciąg, którego można użyć do pobrania odpowiednich zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="1f91a-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="1f91a-135">Można to zrobić, wyprowadzając z `AuthorizeAttribute` i ustawiając właściwość jako `Age` przewinięcie `AuthorizeAttribute.Policy` właściwości.</span><span class="sxs-lookup"><span data-stu-id="1f91a-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="1f91a-136">Ten typ atrybutu ma `Policy` ciąg oparty na zakodowanym prefiksie ( `"MinimumAge"` ) i liczbie całkowitej przekazanej za pośrednictwem konstruktora.</span><span class="sxs-lookup"><span data-stu-id="1f91a-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="1f91a-137">Można zastosować go do akcji w taki sam sposób, jak inne `Authorize` atrybuty, z wyjątkiem tego, że przyjmuje jako parametr liczbę całkowitą.</span><span class="sxs-lookup"><span data-stu-id="1f91a-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="1f91a-138">Niestandardowy IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="1f91a-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="1f91a-139">Niestandardowe `MinimumAgeAuthorizeAttribute` ułatwiają żądania zasad autoryzacji w przypadku dowolnych minimalnych wieku.</span><span class="sxs-lookup"><span data-stu-id="1f91a-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="1f91a-140">Następnym problemem do rozwiązania jest upewnienie się, że zasady autoryzacji są dostępne dla wszystkich tych różnych okresów.</span><span class="sxs-lookup"><span data-stu-id="1f91a-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="1f91a-141">Jest to miejsce, w którym `IAuthorizationPolicyProvider` jest to przydatne.</span><span class="sxs-lookup"><span data-stu-id="1f91a-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="1f91a-142">W przypadku korzystania `MinimumAgeAuthorizationAttribute` z programu nazwy zasad autoryzacji będą zgodne ze wzorcem `"MinimumAge" + Age` , więc niestandardowe `IAuthorizationPolicyProvider` powinny generować zasady autoryzacji, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="1f91a-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="1f91a-143">Analizowanie wieku z nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="1f91a-144">`AuthorizationPolicyBuilder`Tworzenie nowego`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="1f91a-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="1f91a-145">W tym i następujących przykładach zostanie przyjęty, że użytkownik jest uwierzytelniany za pośrednictwem :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="1f91a-145">In this and following examples it will be assumed that the user is authenticated via a :::no-loc(cookie):::.</span></span> <span data-ttu-id="1f91a-146">`AuthorizationPolicyBuilder`Należy utworzyć co najmniej jedną nazwę schematu autoryzacji lub zawsze powiodła się.</span><span class="sxs-lookup"><span data-stu-id="1f91a-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="1f91a-147">W przeciwnym razie nie ma informacji o sposobie zapewnienia użytkownikowi wyzwania i zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="1f91a-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="1f91a-148">Dodawanie wymagań do zasad na podstawie wieku w programie `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="1f91a-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="1f91a-149">W innych scenariuszach można użyć `RequireClaim` , `RequireRole` lub `RequireUserName` zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="1f91a-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="1f91a-150">Wielu dostawców zasad autoryzacji</span><span class="sxs-lookup"><span data-stu-id="1f91a-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="1f91a-151">W przypadku korzystania z `IAuthorizationPolicyProvider` implementacji niestandardowych należy pamiętać, że ASP.NET Core używa tylko jednego wystąpienia `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="1f91a-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="1f91a-152">Jeśli dostawca niestandardowy nie jest w stanie zapewnić zasad autoryzacji dla wszystkich nazw zasad, które będą używane, należy odroczyć dostawcę kopii zapasowych.</span><span class="sxs-lookup"><span data-stu-id="1f91a-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="1f91a-153">Rozważmy na przykład aplikację, która wymaga zarówno niestandardowych zasad wieku, jak i bardziej tradycyjnych operacji pobierania zasad opartych na rolach.</span><span class="sxs-lookup"><span data-stu-id="1f91a-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="1f91a-154">Taka aplikacja może użyć niestandardowego dostawcy zasad autoryzacji, który:</span><span class="sxs-lookup"><span data-stu-id="1f91a-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="1f91a-155">Próbuje przeanalizować nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="1f91a-156">Wywołuje innego dostawcę zasad (na przykład `DefaultAuthorizationPolicyProvider` ), jeśli nazwa zasad nie zawiera wieku.</span><span class="sxs-lookup"><span data-stu-id="1f91a-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="1f91a-157">Przykładowa `IAuthorizationPolicyProvider` implementacja pokazana powyżej może zostać zaktualizowana w celu użycia `DefaultAuthorizationPolicyProvider` przez utworzenie dostawcy zasad tworzenia kopii zapasowych w konstruktorze (do użycia w przypadku, gdy nazwa zasad nie jest zgodna z oczekiwanym wzorcem "minimalnie" + wiek).</span><span class="sxs-lookup"><span data-stu-id="1f91a-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="1f91a-158">Następnie `GetPolicyAsync` można zaktualizować metodę, aby użyć `BackupPolicyProvider` zamiast zwracanej wartości null:</span><span class="sxs-lookup"><span data-stu-id="1f91a-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="1f91a-159">Zasady domyślne</span><span class="sxs-lookup"><span data-stu-id="1f91a-159">Default policy</span></span>

<span data-ttu-id="1f91a-160">Oprócz dostarczania nazwanych zasad autoryzacji, niestandardową `IAuthorizationPolicyProvider` potrzebą jest wdrożenie `GetDefaultPolicyAsync` w celu udostępnienia zasad autoryzacji dla `[Authorize]` atrybutów bez określonej nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="1f91a-161">W wielu przypadkach ten atrybut autoryzacji wymaga tylko uwierzytelnionego użytkownika, więc można wykonać niezbędne zasady z wywołaniem `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="1f91a-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="1f91a-162">Podobnie jak w przypadku wszystkich aspektów niestandardowych `IAuthorizationPolicyProvider` , można je dostosować w razie konieczności.</span><span class="sxs-lookup"><span data-stu-id="1f91a-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="1f91a-163">W niektórych przypadkach może być wskazane pobranie domyślnych zasad z rezerwy `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="1f91a-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="1f91a-164">Zasady powrotu</span><span class="sxs-lookup"><span data-stu-id="1f91a-164">Fallback policy</span></span>

<span data-ttu-id="1f91a-165">Niestandardowe `IAuthorizationPolicyProvider` można opcjonalnie zaimplementować, `GetFallbackPolicyAsync` Aby określić zasady, które są używane podczas [łączenia zasad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) i gdy nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="1f91a-166">Jeśli `GetFallbackPolicyAsync` zwraca zasadę o wartości innej niż null, zwracane zasady są używane przez oprogramowanie pośredniczące autoryzacji, gdy dla żądania nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="1f91a-167">Jeśli nie są wymagane żadne zasady powrotu dostawcy, dostawca może zwrócić `null` lub odroczyć dostawcę rezerwowego:</span><span class="sxs-lookup"><span data-stu-id="1f91a-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="1f91a-168">Użyj niestandardowego IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="1f91a-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="1f91a-169">Aby używać zasad niestandardowych z `IAuthorizationPolicyProvider` , należy \* **musi** _:</span><span class="sxs-lookup"><span data-stu-id="1f91a-169">To use custom policies from an `IAuthorizationPolicyProvider`, you \* **must** _:</span></span>

<span data-ttu-id="1f91a-170">_ Rejestruje odpowiednie `AuthorizationHandler` typy z iniekcją zależności (opisany w [autoryzacji opartej na zasadach](xref:security/authorization/policies#authorization-handlers)), podobnie jak w przypadku wszystkich scenariuszy autoryzacji opartych na zasadach.</span><span class="sxs-lookup"><span data-stu-id="1f91a-170">_ Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="1f91a-171">Zarejestruj typ niestandardowy `IAuthorizationPolicyProvider` w kolekcji usługi iniekcji zależności aplikacji w programie `Startup.ConfigureServices` , aby zastąpić domyślnego dostawcę zasad.</span><span class="sxs-lookup"><span data-stu-id="1f91a-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="1f91a-172">Pełny niestandardowy `IAuthorizationPolicyProvider` przykład jest dostępny w repozytorium programu [dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="1f91a-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
