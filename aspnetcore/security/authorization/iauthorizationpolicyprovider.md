---
title: Dostawcy zasad autoryzacji niestandardowej w ASP.NET Core
author: mjrousos
description: Dowiedz się, jak używać niestandardowego IAuthorizationPolicyProvider w aplikacji ASP.NET Core do dynamicznego generowania zasad autoryzacji.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661771"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Dostawcy zasad autoryzacji niestandardowej przy użyciu usługi IAuthorizationPolicyProvider w ASP.NET Core 

Przez [Mike Rousos](https://github.com/mjrousos)

Zazwyczaj podczas korzystania z [autoryzacji opartej na zasadach](xref:security/authorization/policies)zasady są rejestrowane przez wywołanie `AuthorizationOptions.AddPolicy` jako część konfiguracji usługi autoryzacji. W niektórych scenariuszach może nie być możliwe (lub pożądane) zarejestrowanie wszystkich zasad autoryzacji w ten sposób. W takich przypadkach można użyć `IAuthorizationPolicyProvider` niestandardowego do kontrolowania sposobu podajesz zasady autoryzacji.

Przykłady scenariuszy, w których [niestandardowe IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) może być przydatne obejmują:

* Korzystanie z usługi zewnętrznej w celu zapewnienia oceny zasad.
* Korzystanie z szerokiego zakresu zasad (na przykład dla różnych numerów lub grup wiekowych) nie `AuthorizationOptions.AddPolicy` ma sensu dodawać poszczególnych zasad autoryzacji za pomocą połączenia.
* Tworzenie zasad w czasie wykonywania na podstawie informacji w zewnętrznym źródle danych (takich jak baza danych) lub dynamiczne określanie wymagań dotyczących autoryzacji za pomocą innego mechanizmu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) z [repozytorium AspNetCore GitHub](https://github.com/dotnet/AspNetCore). Pobierz plik ZIP repozytorium dotnet/AspNetCore. Rozpaj plik. Przejdź do folderu projektu *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Dostosowywanie pobierania zasad

ASP.NET aplikacje Core używają implementacji `IAuthorizationPolicyProvider` interfejsu do pobierania zasad autoryzacji. Domyślnie [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) jest zarejestrowany i używany. `DefaultAuthorizationPolicyProvider`zwraca zasady `AuthorizationOptions` z podanych w wywołaniu. `IServiceCollection.AddAuthorization`

Dostosuj to zachowanie, rejestrując `IAuthorizationPolicyProvider` inną implementację w kontenerze [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. 

Interfejs `IAuthorizationPolicyProvider` zawiera trzy interfejsy API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) zwraca zasady autoryzacji dla danej nazwy.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) zwraca domyślne zasady autoryzacji `[Authorize]` (zasady używane dla atrybutów bez określonych zasad). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) zwraca zasady autoryzacji rezerwowej (zasady używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad). 

Implementując te interfejsy API, można dostosować sposób, w jaki zasady autoryzacji są dostarczane.

## <a name="parameterized-authorize-attribute-example"></a>Przykład atrybutu autoryzacji sparametryzowanej

Jednym ze `IAuthorizationPolicyProvider` scenariuszy, w `[Authorize]` którym jest przydatne jest włączenie atrybutów niestandardowych, których wymagania zależą od parametru. Na przykład w dokumentacji [autoryzacji opartej na zasadach](xref:security/authorization/policies) jako przykładowa została użyta zasada oparta na wieku ("AtLeast21"). Jeśli różne akcje kontrolera w aplikacji powinny być dostępne dla użytkowników w *różnym* wieku, może być przydatne, aby mieć wiele różnych zasad opartych na wieku. Zamiast rejestrować wszystkie różne zasady oparte na wieku, `AuthorizationOptions`które aplikacja będzie potrzebować w , `IAuthorizationPolicyProvider`można wygenerować zasady dynamicznie za pomocą niestandardowego . Aby ułatwić korzystanie z zasad, można dodawać adnotacje `[MinimumAgeAuthorize(20)]`do akcji z atrybutem autoryzacji niestandardowej, takim jak .

## <a name="custom-authorization-attributes"></a>Atrybuty autoryzacji niestandardowej

Zasady autoryzacji są identyfikowane przez ich nazwy. Niestandardowe `MinimumAgeAuthorizeAttribute` opisane wcześniej musi mapować argumenty w ciąg, który może służyć do pobierania odpowiednich zasad autoryzacji. Można to zrobić, wyprowadzając `AuthorizeAttribute` z `Age` i `AuthorizeAttribute.Policy` co właściwość zawinąć właściwość.

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

Ten typ atrybutu `Policy` ma ciąg oparty na prefiksie zakodowany (`"MinimumAge"`) i liczba całkowita przekazywane za pośrednictwem konstruktora.

Można go zastosować do akcji w `Authorize` taki sam sposób jak inne atrybuty, z tą różnicą, że przyjmuje całkowitą jako parametr.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Niestandardowa autoryzacja IAuthorizationPolicyProvider

Niestandardowe `MinimumAgeAuthorizeAttribute` ułatwia żądanie zasad autoryzacji dla dowolnego wieku minimalnego pożądanego. Kolejnym problemem do rozwiązania jest upewnienie się, że zasady autoryzacji są dostępne dla wszystkich osób w różnym wieku. To jest, `IAuthorizationPolicyProvider` gdy jest przydatna.

Podczas `MinimumAgeAuthorizationAttribute`korzystania, nazwy zasad autoryzacji `"MinimumAge" + Age`będą zgodne `IAuthorizationPolicyProvider` ze wzorcem, więc niestandardowe powinny generować zasady autoryzacji przez:

* Analizowanie wieku od nazwy zasad.
* Używanie `AuthorizationPolicyBuilder` do tworzenia nowego`AuthorizationPolicy`
* W tym i następujących przykładach zakłada się, że użytkownik jest uwierzytelniony za pomocą pliku cookie. Powinien `AuthorizationPolicyBuilder` być skonstruowany z co najmniej jedną nazwą schematu autoryzacji lub zawsze powiedzie się. W przeciwnym razie nie ma informacji na temat sposobu zapewnienia wyzwanie dla użytkownika i wyjątek zostanie zgłoszony.
* Dodawanie wymagań do zasad na podstawie `AuthorizationPolicyBuilder.AddRequirements`wieku z . W innych scenariuszach można `RequireClaim` `RequireRole`użyć `RequireUserName` , lub zamiast tego.

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

Korzystając z `IAuthorizationPolicyProvider` niestandardowych implementacji, należy pamiętać, że ASP.NET Core `IAuthorizationPolicyProvider`używa tylko jednego wystąpienia programu . Jeśli dostawca niestandardowy nie jest w stanie podać zasad autoryzacji dla wszystkich nazw zasad, które będą używane, należy odroczyć do dostawcy kopii zapasowej. 

Rozważmy na przykład aplikację, która wymaga zarówno niestandardowych zasad wiekowych, jak i bardziej tradycyjnego pobierania zasad opartych na rolach. Taka aplikacja może używać dostawcy zasad autoryzacji niestandardowej, który:

* Próbuje przeanalizować nazwy zasad. 
* Wywołuje do innego dostawcy `DefaultAuthorizationPolicyProvider`zasad (np.), jeśli nazwa zasad nie zawiera wieku.

Przykładowa `IAuthorizationPolicyProvider` implementacja pokazano powyżej można `DefaultAuthorizationPolicyProvider` zaktualizować, aby użyć przez utworzenie dostawcy zasad tworzenia kopii zapasowych w konstruktorze (do użycia w przypadku, gdy nazwa zasad nie pasuje do oczekiwanego wzorca "MinimumAge" + age).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Następnie `GetPolicyAsync` metoda może zostać zaktualizowana, aby użyć `BackupPolicyProvider` zamiast zwracania null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Zasady domyślne

Oprócz dostarczania nazwanych zasad autoryzacji, `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` niestandardowe musi zaimplementować, aby zapewnić zasady autoryzacji dla `[Authorize]` atrybutów bez określonej nazwy zasad.

W wielu przypadkach ten atrybut autoryzacji wymaga tylko uwierzytelnionego użytkownika, dzięki `RequireAuthenticatedUser`czemu można wprowadzić niezbędne zasady za pomocą połączenia:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Podobnie jak w odniesieniu `IAuthorizationPolicyProvider`do wszystkich aspektów niestandardowego , można dostosować to, w razie potrzeby. W niektórych przypadkach może być pożądane pobranie domyślnej `IAuthorizationPolicyProvider`zasady z rezerwowego .

## <a name="fallback-policy"></a>Zasady rezerwowe

Niestandardowe `IAuthorizationPolicyProvider` można opcjonalnie zaimplementować `GetFallbackPolicyAsync` w celu zapewnienia zasad, które są używane podczas [łączenia zasad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) i gdy nie określono żadnych zasad. Jeśli `GetFallbackPolicyAsync` zwraca zasady inne niż null, zwrócone zasady są używane przez oprogramowanie pośredniczące autoryzacji, gdy nie określono żadnych zasad dla żądania.

Jeśli nie jest wymagana żadna zasada `null` rezerwowa, dostawca może zwrócić lub odroczyć do dostawcy rezerwowego:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Używanie niestandardowego programu IAuthorizationPolicyProvider

Aby używać zasad `IAuthorizationPolicyProvider`niestandardowych z programu , należy:

* Zarejestruj odpowiednie `AuthorizationHandler` typy z iniekcji zależności (opisane w [autoryzacji opartej na zasadach),](xref:security/authorization/policies#authorization-handlers)jak w przypadku wszystkich scenariuszy autoryzacji opartych na zasadach.
* Zarejestruj typ `IAuthorizationPolicyProvider` niestandardowy w kolekcji usługi iniekcji zależności aplikacji (w), `Startup.ConfigureServices`aby zastąpić domyślnego dostawcę zasad.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Kompletny próbka niestandardowa `IAuthorizationPolicyProvider` jest dostępna w [repozytorium dotnet/aspnetcore GitHub.](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)
