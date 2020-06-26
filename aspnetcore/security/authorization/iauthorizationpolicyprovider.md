---
title: Niestandardowi dostawcy zasad autoryzacji w ASP.NET Core
author: mjrousos
description: Dowiedz się, jak używać niestandardowych IAuthorizationPolicyProvider w aplikacji ASP.NET Core do dynamicznego generowania zasad autoryzacji.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: bb9b52da08639680b05a102dd4df71ff1af00971
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399559"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Niestandardowi dostawcy zasad autoryzacji korzystający z usługi IAuthorizationPolicyProvider w ASP.NET Core 

Według [Jan Rousos](https://github.com/mjrousos)

Zwykle podczas korzystania z [autoryzacji opartej na zasadach](xref:security/authorization/policies)zasady są rejestrowane przez wywołanie `AuthorizationOptions.AddPolicy` w ramach konfiguracji usługi autoryzacji. W niektórych scenariuszach może nie być możliwe (lub pożądane) rejestrowanie wszystkich zasad autoryzacji w ten sposób. W takich przypadkach można [użyć niestandardowych `IAuthorizationPolicyProvider` ](#ci) do kontrolowania sposobu dostarczania zasad autoryzacji.

Przykłady scenariuszy, w których może być przydatne niestandardowe [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) :

* Korzystanie z usługi zewnętrznej w celu zapewnienia oceny zasad.
* Użycie dużego zakresu zasad (na przykład w przypadku różnych numerów pomieszczeń lub wieku), dlatego nie ma sensu dodania poszczególnych zasad autoryzacji do `AuthorizationOptions.AddPolicy` wywołania.
* Tworzenie zasad w czasie wykonywania na podstawie informacji w zewnętrznym źródle danych (np. bazy danych) lub Określanie wymagań dotyczących autoryzacji w sposób dynamiczny przez inny mechanizm.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) z [repozytorium GitHub AspNetCore](https://github.com/dotnet/AspNetCore). Pobierz plik ZIP repozytorium dotnet/AspNetCore. Rozpakuj plik. Przejdź do folderu *src/Security/Samples/CustomPolicyProvider* Project.

## <a name="customize-policy-retrieval"></a>Dostosuj pobieranie zasad

Aplikacje ASP.NET Core korzystają z implementacji `IAuthorizationPolicyProvider` interfejsu w celu pobierania zasad autoryzacji. Domyślnie [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) jest zarejestrowany i używany. `DefaultAuthorizationPolicyProvider`zwraca zasady z `AuthorizationOptions` podanego w `IServiceCollection.AddAuthorization` wywołaniu.

Dostosuj to zachowanie, rejestrując inną `IAuthorizationPolicyProvider` implementację w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. 

`IAuthorizationPolicyProvider`Interfejs zawiera trzy interfejsy API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) zwraca zasady autoryzacji dla danej nazwy.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) zwraca domyślne zasady autoryzacji (zasady używane dla `[Authorize]` atrybutów bez określonych zasad). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) zwraca rezerwowe zasady autoryzacji (zasady używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad). 

Implementując te interfejsy API, można dostosować sposób, w jaki są udostępniane zasady autoryzacji.

## <a name="parameterized-authorize-attribute-example"></a>Przykładowy atrybut autoryzacji sparametryzowanej

Jednym z scenariuszy, gdzie `IAuthorizationPolicyProvider` jest przydatna, jest włączenie atrybutów niestandardowych, `[Authorize]` których wymagania zależą od parametru. Na przykład w dokumentacji dotyczącej [autoryzacji opartej na zasadach](xref:security/authorization/policies) , jako przykład użyto zasad opartych na wieku ("AtLeast21"). Jeśli różne akcje kontrolera w aplikacji powinny być dostępne dla użytkowników z *różnych* okresów obowiązywania, może być przydatne w wielu różnych zasadach opartych na wieku. Zamiast zarejestrowania wszystkich zasad opartych na wieku, do których aplikacja będzie potrzebna `AuthorizationOptions` , możesz dynamicznie generować zasady za pomocą niestandardowej `IAuthorizationPolicyProvider` . Aby ułatwić korzystanie z zasad, można dodawać adnotacje do akcji przy użyciu niestandardowego atrybutu autoryzacji, takiego jak `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>Niestandardowe atrybuty autoryzacji

Zasady autoryzacji są identyfikowane przez ich nazwy. Niestandardowe `MinimumAgeAuthorizeAttribute` opisane wcześniej muszą mapować argumenty na ciąg, którego można użyć do pobrania odpowiednich zasad autoryzacji. Można to zrobić, wyprowadzając z `AuthorizeAttribute` i ustawiając właściwość jako `Age` przewinięcie `AuthorizeAttribute.Policy` właściwości.

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

Ten typ atrybutu ma `Policy` ciąg oparty na zakodowanym prefiksie ( `"MinimumAge"` ) i liczbie całkowitej przekazanej za pośrednictwem konstruktora.

Można zastosować go do akcji w taki sam sposób, jak inne `Authorize` atrybuty, z wyjątkiem tego, że przyjmuje jako parametr liczbę całkowitą.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Niestandardowy IAuthorizationPolicyProvider

Niestandardowe `MinimumAgeAuthorizeAttribute` ułatwiają żądania zasad autoryzacji w przypadku dowolnych minimalnych wieku. Następnym problemem do rozwiązania jest upewnienie się, że zasady autoryzacji są dostępne dla wszystkich tych różnych okresów. Jest to miejsce, w którym `IAuthorizationPolicyProvider` jest to przydatne.

W przypadku korzystania `MinimumAgeAuthorizationAttribute` z programu nazwy zasad autoryzacji będą zgodne ze wzorcem `"MinimumAge" + Age` , więc niestandardowe `IAuthorizationPolicyProvider` powinny generować zasady autoryzacji, wykonując następujące czynności:

* Analizowanie wieku z nazwy zasad.
* `AuthorizationPolicyBuilder`Tworzenie nowego`AuthorizationPolicy`
* W tym i następujących przykładach zostanie przyjęty, że użytkownik jest uwierzytelniany za pośrednictwem pliku cookie. `AuthorizationPolicyBuilder`Należy utworzyć co najmniej jedną nazwę schematu autoryzacji lub zawsze powiodła się. W przeciwnym razie nie ma informacji o sposobie zapewnienia użytkownikowi wyzwania i zostanie zgłoszony wyjątek.
* Dodawanie wymagań do zasad na podstawie wieku w programie `AuthorizationPolicyBuilder.AddRequirements` . W innych scenariuszach można użyć `RequireClaim` , `RequireRole` lub `RequireUserName` zamiast tego.

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

## <a name="multiple-authorization-policy-providers"></a>Wielu dostawców zasad autoryzacji

W przypadku korzystania z `IAuthorizationPolicyProvider` implementacji niestandardowych należy pamiętać, że ASP.NET Core używa tylko jednego wystąpienia `IAuthorizationPolicyProvider` . Jeśli dostawca niestandardowy nie jest w stanie zapewnić zasad autoryzacji dla wszystkich nazw zasad, które będą używane, należy odroczyć dostawcę kopii zapasowych. 

Rozważmy na przykład aplikację, która wymaga zarówno niestandardowych zasad wieku, jak i bardziej tradycyjnych operacji pobierania zasad opartych na rolach. Taka aplikacja może użyć niestandardowego dostawcy zasad autoryzacji, który:

* Próbuje przeanalizować nazwy zasad. 
* Wywołuje innego dostawcę zasad (na przykład `DefaultAuthorizationPolicyProvider` ), jeśli nazwa zasad nie zawiera wieku.

Przykładowa `IAuthorizationPolicyProvider` implementacja pokazana powyżej może zostać zaktualizowana w celu użycia `DefaultAuthorizationPolicyProvider` przez utworzenie dostawcy zasad tworzenia kopii zapasowych w konstruktorze (do użycia w przypadku, gdy nazwa zasad nie jest zgodna z oczekiwanym wzorcem "minimalnie" + wiek).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Następnie `GetPolicyAsync` można zaktualizować metodę, aby użyć `BackupPolicyProvider` zamiast zwracanej wartości null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Zasady domyślne

Oprócz dostarczania nazwanych zasad autoryzacji, niestandardową `IAuthorizationPolicyProvider` potrzebą jest wdrożenie `GetDefaultPolicyAsync` w celu udostępnienia zasad autoryzacji dla `[Authorize]` atrybutów bez określonej nazwy zasad.

W wielu przypadkach ten atrybut autoryzacji wymaga tylko uwierzytelnionego użytkownika, więc można wykonać niezbędne zasady z wywołaniem `RequireAuthenticatedUser` :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Podobnie jak w przypadku wszystkich aspektów niestandardowych `IAuthorizationPolicyProvider` , można je dostosować w razie konieczności. W niektórych przypadkach może być wskazane pobranie domyślnych zasad z rezerwy `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>Zasady powrotu

Niestandardowe `IAuthorizationPolicyProvider` można opcjonalnie zaimplementować, `GetFallbackPolicyAsync` Aby określić zasady, które są używane podczas [łączenia zasad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) i gdy nie określono żadnych zasad. Jeśli `GetFallbackPolicyAsync` zwraca zasadę o wartości innej niż null, zwracane zasady są używane przez oprogramowanie pośredniczące autoryzacji, gdy dla żądania nie określono żadnych zasad.

Jeśli nie są wymagane żadne zasady powrotu dostawcy, dostawca może zwrócić `null` lub odroczyć dostawcę rezerwowego:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Użyj niestandardowego IAuthorizationPolicyProvider

Aby używać zasad niestandardowych z poziomu programu `IAuthorizationPolicyProvider` , ***należy***:

* Zarejestruj odpowiednie `AuthorizationHandler` typy z iniekcją zależności (zgodnie z opisem w [autoryzacji opartej na zasadach](xref:security/authorization/policies#authorization-handlers)), podobnie jak w przypadku wszystkich scenariuszy autoryzacji opartych na zasadach.
* Zarejestruj typ niestandardowy `IAuthorizationPolicyProvider` w kolekcji usługi iniekcji zależności aplikacji w programie `Startup.ConfigureServices` , aby zastąpić domyślnego dostawcę zasad.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

Pełny niestandardowy `IAuthorizationPolicyProvider` przykład jest dostępny w repozytorium programu [dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)w witrynie GitHub.
