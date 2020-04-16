---
title: Dostawcy zasad autoryzacji niestandardowej w ASP.NET Core
author: mjrousos
description: Dowiedz się, jak używać niestandardowego IAuthorizationPolicyProvider w aplikacji ASP.NET Core do dynamicznego generowania zasad autoryzacji.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440925"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="0d224-103">Dostawcy zasad autoryzacji niestandardowej przy użyciu usługi IAuthorizationPolicyProvider w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d224-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="0d224-104">Przez [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="0d224-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="0d224-105">Zazwyczaj podczas korzystania z [autoryzacji opartej na zasadach](xref:security/authorization/policies)zasady są rejestrowane przez wywołanie `AuthorizationOptions.AddPolicy` jako część konfiguracji usługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0d224-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="0d224-106">W niektórych scenariuszach może nie być możliwe (lub pożądane) zarejestrowanie wszystkich zasad autoryzacji w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="0d224-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="0d224-107">W takich przypadkach można użyć `IAuthorizationPolicyProvider` niestandardowego do kontrolowania sposobu podajesz zasady autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0d224-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="0d224-108">Przykłady scenariuszy, w których [niestandardowe IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) może być przydatne obejmują:</span><span class="sxs-lookup"><span data-stu-id="0d224-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="0d224-109">Korzystanie z usługi zewnętrznej w celu zapewnienia oceny zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="0d224-110">Korzystanie z szerokiego zakresu zasad (na przykład dla różnych numerów lub grup wiekowych) nie `AuthorizationOptions.AddPolicy` ma sensu dodawać poszczególnych zasad autoryzacji za pomocą połączenia.</span><span class="sxs-lookup"><span data-stu-id="0d224-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="0d224-111">Tworzenie zasad w czasie wykonywania na podstawie informacji w zewnętrznym źródle danych (takich jak baza danych) lub dynamiczne określanie wymagań dotyczących autoryzacji za pomocą innego mechanizmu.</span><span class="sxs-lookup"><span data-stu-id="0d224-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="0d224-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [repozytorium AspNetCore GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="0d224-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="0d224-113">Pobierz plik ZIP repozytorium dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="0d224-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="0d224-114">Rozpaj plik.</span><span class="sxs-lookup"><span data-stu-id="0d224-114">Unzip the file.</span></span> <span data-ttu-id="0d224-115">Przejdź do folderu projektu *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="0d224-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="0d224-116">Dostosowywanie pobierania zasad</span><span class="sxs-lookup"><span data-stu-id="0d224-116">Customize policy retrieval</span></span>

<span data-ttu-id="0d224-117">ASP.NET aplikacje Core używają implementacji `IAuthorizationPolicyProvider` interfejsu do pobierania zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0d224-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="0d224-118">Domyślnie [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) jest zarejestrowany i używany.</span><span class="sxs-lookup"><span data-stu-id="0d224-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="0d224-119">`DefaultAuthorizationPolicyProvider`zwraca zasady `AuthorizationOptions` z podanych w wywołaniu. `IServiceCollection.AddAuthorization`</span><span class="sxs-lookup"><span data-stu-id="0d224-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="0d224-120">Dostosuj to zachowanie, rejestrując `IAuthorizationPolicyProvider` inną implementację w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0d224-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="0d224-121">Interfejs `IAuthorizationPolicyProvider` zawiera trzy interfejsy API:</span><span class="sxs-lookup"><span data-stu-id="0d224-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="0d224-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) zwraca zasady autoryzacji dla danej nazwy.</span><span class="sxs-lookup"><span data-stu-id="0d224-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="0d224-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) zwraca domyślne zasady autoryzacji `[Authorize]` (zasady używane dla atrybutów bez określonych zasad).</span><span class="sxs-lookup"><span data-stu-id="0d224-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="0d224-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) zwraca zasady autoryzacji rezerwowej (zasady używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad).</span><span class="sxs-lookup"><span data-stu-id="0d224-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="0d224-125">Implementując te interfejsy API, można dostosować sposób, w jaki zasady autoryzacji są dostarczane.</span><span class="sxs-lookup"><span data-stu-id="0d224-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="0d224-126">Przykład atrybutu autoryzacji sparametryzowanej</span><span class="sxs-lookup"><span data-stu-id="0d224-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="0d224-127">Jednym ze `IAuthorizationPolicyProvider` scenariuszy, w `[Authorize]` którym jest przydatne jest włączenie atrybutów niestandardowych, których wymagania zależą od parametru.</span><span class="sxs-lookup"><span data-stu-id="0d224-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="0d224-128">Na przykład w dokumentacji [autoryzacji opartej na zasadach](xref:security/authorization/policies) jako przykładowa została użyta zasada oparta na wieku ("AtLeast21").</span><span class="sxs-lookup"><span data-stu-id="0d224-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="0d224-129">Jeśli różne akcje kontrolera w aplikacji powinny być dostępne dla użytkowników w *różnym* wieku, może być przydatne, aby mieć wiele różnych zasad opartych na wieku.</span><span class="sxs-lookup"><span data-stu-id="0d224-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="0d224-130">Zamiast rejestrować wszystkie różne zasady oparte na wieku, `AuthorizationOptions`które aplikacja będzie potrzebować w , `IAuthorizationPolicyProvider`można wygenerować zasady dynamicznie za pomocą niestandardowego .</span><span class="sxs-lookup"><span data-stu-id="0d224-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="0d224-131">Aby ułatwić korzystanie z zasad, można dodawać adnotacje `[MinimumAgeAuthorize(20)]`do akcji z atrybutem autoryzacji niestandardowej, takim jak .</span><span class="sxs-lookup"><span data-stu-id="0d224-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="0d224-132">Atrybuty autoryzacji niestandardowej</span><span class="sxs-lookup"><span data-stu-id="0d224-132">Custom Authorization attributes</span></span>

<span data-ttu-id="0d224-133">Zasady autoryzacji są identyfikowane przez ich nazwy.</span><span class="sxs-lookup"><span data-stu-id="0d224-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="0d224-134">Niestandardowe `MinimumAgeAuthorizeAttribute` opisane wcześniej musi mapować argumenty w ciąg, który może służyć do pobierania odpowiednich zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="0d224-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="0d224-135">Można to zrobić, wyprowadzając `AuthorizeAttribute` z `Age` i `AuthorizeAttribute.Policy` co właściwość zawinąć właściwość.</span><span class="sxs-lookup"><span data-stu-id="0d224-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="0d224-136">Ten typ atrybutu `Policy` ma ciąg oparty na prefiksie zakodowany (`"MinimumAge"`) i liczba całkowita przekazywane za pośrednictwem konstruktora.</span><span class="sxs-lookup"><span data-stu-id="0d224-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="0d224-137">Można go zastosować do akcji w `Authorize` taki sam sposób jak inne atrybuty, z tą różnicą, że przyjmuje całkowitą jako parametr.</span><span class="sxs-lookup"><span data-stu-id="0d224-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="0d224-138">Niestandardowa autoryzacja IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="0d224-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="0d224-139">Niestandardowe `MinimumAgeAuthorizeAttribute` ułatwia żądanie zasad autoryzacji dla dowolnego wieku minimalnego pożądanego.</span><span class="sxs-lookup"><span data-stu-id="0d224-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="0d224-140">Kolejnym problemem do rozwiązania jest upewnienie się, że zasady autoryzacji są dostępne dla wszystkich osób w różnym wieku.</span><span class="sxs-lookup"><span data-stu-id="0d224-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="0d224-141">To jest, `IAuthorizationPolicyProvider` gdy jest przydatna.</span><span class="sxs-lookup"><span data-stu-id="0d224-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="0d224-142">Podczas `MinimumAgeAuthorizationAttribute`korzystania, nazwy zasad autoryzacji `"MinimumAge" + Age`będą zgodne `IAuthorizationPolicyProvider` ze wzorcem, więc niestandardowe powinny generować zasady autoryzacji przez:</span><span class="sxs-lookup"><span data-stu-id="0d224-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="0d224-143">Analizowanie wieku od nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="0d224-144">Używanie `AuthorizationPolicyBuilder` do tworzenia nowego`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="0d224-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="0d224-145">W tym i następujących przykładach zakłada się, że użytkownik jest uwierzytelniony za pomocą pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="0d224-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="0d224-146">Powinien `AuthorizationPolicyBuilder` być skonstruowany z co najmniej jedną nazwą schematu autoryzacji lub zawsze powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="0d224-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="0d224-147">W przeciwnym razie nie ma informacji na temat sposobu zapewnienia wyzwanie dla użytkownika i wyjątek zostanie zgłoszony.</span><span class="sxs-lookup"><span data-stu-id="0d224-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="0d224-148">Dodawanie wymagań do zasad na podstawie `AuthorizationPolicyBuilder.AddRequirements`wieku z .</span><span class="sxs-lookup"><span data-stu-id="0d224-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="0d224-149">W innych scenariuszach można `RequireClaim` `RequireRole`użyć `RequireUserName` , lub zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="0d224-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="0d224-150">Wielu dostawców zasad autoryzacji</span><span class="sxs-lookup"><span data-stu-id="0d224-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="0d224-151">Korzystając z `IAuthorizationPolicyProvider` niestandardowych implementacji, należy pamiętać, że ASP.NET Core `IAuthorizationPolicyProvider`używa tylko jednego wystąpienia programu .</span><span class="sxs-lookup"><span data-stu-id="0d224-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="0d224-152">Jeśli dostawca niestandardowy nie jest w stanie podać zasad autoryzacji dla wszystkich nazw zasad, które będą używane, należy odroczyć do dostawcy kopii zapasowej.</span><span class="sxs-lookup"><span data-stu-id="0d224-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="0d224-153">Rozważmy na przykład aplikację, która wymaga zarówno niestandardowych zasad wiekowych, jak i bardziej tradycyjnego pobierania zasad opartych na rolach.</span><span class="sxs-lookup"><span data-stu-id="0d224-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="0d224-154">Taka aplikacja może używać dostawcy zasad autoryzacji niestandardowej, który:</span><span class="sxs-lookup"><span data-stu-id="0d224-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="0d224-155">Próbuje przeanalizować nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="0d224-156">Wywołuje do innego dostawcy `DefaultAuthorizationPolicyProvider`zasad (np.), jeśli nazwa zasad nie zawiera wieku.</span><span class="sxs-lookup"><span data-stu-id="0d224-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="0d224-157">Przykładowa `IAuthorizationPolicyProvider` implementacja pokazano powyżej można `DefaultAuthorizationPolicyProvider` zaktualizować, aby użyć przez utworzenie dostawcy zasad tworzenia kopii zapasowych w konstruktorze (do użycia w przypadku, gdy nazwa zasad nie pasuje do oczekiwanego wzorca "MinimumAge" + age).</span><span class="sxs-lookup"><span data-stu-id="0d224-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="0d224-158">Następnie `GetPolicyAsync` metoda może zostać zaktualizowana, aby użyć `BackupPolicyProvider` zamiast zwracania null:</span><span class="sxs-lookup"><span data-stu-id="0d224-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="0d224-159">Zasady domyślne</span><span class="sxs-lookup"><span data-stu-id="0d224-159">Default policy</span></span>

<span data-ttu-id="0d224-160">Oprócz dostarczania nazwanych zasad autoryzacji, `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` niestandardowe musi zaimplementować, aby zapewnić zasady autoryzacji dla `[Authorize]` atrybutów bez określonej nazwy zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="0d224-161">W wielu przypadkach ten atrybut autoryzacji wymaga tylko uwierzytelnionego użytkownika, dzięki `RequireAuthenticatedUser`czemu można wprowadzić niezbędne zasady za pomocą połączenia:</span><span class="sxs-lookup"><span data-stu-id="0d224-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="0d224-162">Podobnie jak w odniesieniu `IAuthorizationPolicyProvider`do wszystkich aspektów niestandardowego , można dostosować to, w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="0d224-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="0d224-163">W niektórych przypadkach może być pożądane pobranie domyślnej `IAuthorizationPolicyProvider`zasady z rezerwowego .</span><span class="sxs-lookup"><span data-stu-id="0d224-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="0d224-164">Zasady rezerwowe</span><span class="sxs-lookup"><span data-stu-id="0d224-164">Fallback policy</span></span>

<span data-ttu-id="0d224-165">Niestandardowe `IAuthorizationPolicyProvider` można opcjonalnie zaimplementować `GetFallbackPolicyAsync` w celu zapewnienia zasad, które są używane podczas [łączenia zasad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) i gdy nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="0d224-166">Jeśli `GetFallbackPolicyAsync` zwraca zasady inne niż null, zwrócone zasady są używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad dla żądania.</span><span class="sxs-lookup"><span data-stu-id="0d224-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="0d224-167">Jeśli nie jest wymagana żadna zasada `null` rezerwowa, dostawca może zwrócić lub odroczyć do dostawcy rezerwowego:</span><span class="sxs-lookup"><span data-stu-id="0d224-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="0d224-168">Używanie niestandardowego programu IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="0d224-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="0d224-169">Aby używać zasad `IAuthorizationPolicyProvider`niestandardowych z programu , należy:</span><span class="sxs-lookup"><span data-stu-id="0d224-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="0d224-170">Zarejestruj odpowiednie `AuthorizationHandler` typy z iniekcji zależności (opisane w [autoryzacji opartej na zasadach),](xref:security/authorization/policies#authorization-handlers)jak w przypadku wszystkich scenariuszy autoryzacji opartych na zasadach.</span><span class="sxs-lookup"><span data-stu-id="0d224-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="0d224-171">Zarejestruj typ `IAuthorizationPolicyProvider` niestandardowy w kolekcji usługi iniekcji zależności aplikacji (w), `Startup.ConfigureServices`aby zastąpić domyślnego dostawcę zasad.</span><span class="sxs-lookup"><span data-stu-id="0d224-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="0d224-172">Kompletny próbka niestandardowa `IAuthorizationPolicyProvider` jest dostępna w [repozytorium dotnet/aspnetcore GitHub.](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="0d224-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
