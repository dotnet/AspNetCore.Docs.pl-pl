---
title: Omówienie platformy ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak ASP.NET Core MVC to rozbudowana platforma służąca do tworzenia aplikacji sieci Web i interfejsów API przy użyciu wzorca projektowego modelu widoku.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 29701506d9f6c1ff90081a548d524bc0deb65cda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633620"
---
# <a name="overview-of-aspnet-core-mvc"></a>Omówienie platformy ASP.NET Core MVC

Przez [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC to rozbudowana platforma służąca do tworzenia aplikacji sieci Web i interfejsów API przy użyciu wzorca projektowego modelu widoku.

## <a name="what-is-the-mvc-pattern"></a>Co to jest wzorzec MVC?

Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych grup składników: modeli, widoków i kontrolerów. Ten wzorzec pozwala uzyskać [rozdzielenie obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Korzystając z tego wzorca, żądania użytkowników są kierowane do kontrolera, który jest odpowiedzialny za pracę z modelem w celu wykonywania akcji użytkownika i/lub pobierania wyników zapytań. Kontroler wybierze widok, który ma być wyświetlany użytkownikowi, i udostępnia mu wymagane dane modelu.

Na poniższym diagramie przedstawiono trzy główne składniki, które odwołują się do innych:

![Wzorzec MVC](overview/_static/mvc.png)

Ten podział obowiązków pomaga skalować aplikację pod kątem złożoności, ponieważ ułatwia to kod, debugowanie i testowanie elementu (model, widok lub kontroler), który ma pojedyncze zadanie. Jest trudniejsze, aby aktualizować, testować i debugować kod, który ma zależności rozłożone na dwa lub więcej z tych trzech obszarów. Na przykład logika interfejsu użytkownika zmienia się częściej niż logika biznesowa. Jeśli kod prezentacji i logika biznesowa są łączone w pojedynczym obiekcie, obiekt zawierający logikę biznesową musi być modyfikowany za każdym razem, gdy interfejs użytkownika jest zmieniany. Często wprowadza błędy i wymaga przetestowania logiki biznesowej po każdym minimalnym zmianie interfejsu użytkownika.

> [!NOTE]
> Zarówno widok, jak i kontroler zależą od modelu. Jednak model jest zależny od widoku, a nie kontrolera. Jest to jedna z najważniejszych zalet separacji. Ta separacja umożliwia skompilowanie i przetestowanie modelu niezależnie od prezentacji wizualnej.

### <a name="model-responsibilities"></a>Obowiązki związane z modelem

Model w aplikacji MVC reprezentuje stan aplikacji i wszelkie operacje logiki biznesowej, które powinny być wykonywane przez nią. Logika biznesowa powinna być hermetyzowana w modelu wraz z dowolnymi regułami implementacji dotyczącymi utrwalania stanu aplikacji. Widoki z jednoznacznie określonymi typami zazwyczaj używają typów ViewModel zaprojektowanych do przechowywania danych do wyświetlenia w tym widoku. Kontroler tworzy i wypełnia te wystąpienia ViewModel z modelu.

### <a name="view-responsibilities"></a>Wyświetl obowiązki

Widoki są odpowiedzialne za prezentowanie zawartości za pomocą interfejsu użytkownika. Używają one [ Razor aparatu widoku](#razor-view-engine) do osadzania kodu platformy .NET w znaczniku html. W widokach powinna być minimalna logika, a jakakolwiek logika powinna odnosić się do treści prezentacji. Jeśli okaże się, że trzeba wykonać znaczną transakcję logiki w plikach widoku, aby wyświetlić dane z modelu złożonego, należy rozważyć użycie [składnika widoku](views/view-components.md), ViewModel lub szablonu widoku, aby uprościć widok.

### <a name="controller-responsibilities"></a>Obowiązki kontrolera

Kontrolery są składnikami, które obsługują interakcję z użytkownikiem, pracują z modelem i ostatecznie wybierają widok do renderowania. W aplikacji MVC widok wyświetla tylko informacje; kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje. We wzorcu MVC kontroler jest punktem wejścia, który jest odpowiedzialny za wybór typów modelu do pracy i widok do renderowania (w związku z czym jego nazwa określa, jak aplikacja odpowiada na daną prośbę).

> [!NOTE]
> Kontrolery nie powinny być nadmiernie skomplikowane przez zbyt wiele obowiązków. Aby zachować logikę kontrolera przed nadmierną złożonością, wypychanie logiki biznesowej z kontrolera i do modelu domeny.

>[!TIP]
> Jeśli okaże się, że akcje wykonywane przez kontroler często wykonują tego samego rodzaju akcje, przenieś te typowe akcje do [filtrów](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Co to jest ASP.NET Core MVC

ASP.NET Core MVC Framework jest lekkim, wysoce weryfikowalne środowiskiem prezentacji zoptymalizowanym pod kątem używania z ASP.NET Core.

ASP.NET Core MVC oferuje oparty na wzorcach sposób tworzenia dynamicznych witryn sieci Web, które umożliwiają czyste rozdzielenie problemów. Zapewnia pełną kontrolę nad znacznikiem, obsługuje programowanie przyjaznych dla TDD i używa najnowszych standardów sieci Web.

## <a name="features"></a>Funkcje

ASP.NET Core MVC obejmuje następujące elementy:

* [Routing](#routing)
* [Powiązanie modelu](#model-binding)
* [Walidacja modelu](#model-validation)
* [Wstrzykiwanie zależności](../fundamentals/dependency-injection.md)
* [Filtry](#filters)
* [Obszary](#areas)
* [Interfejsy API sieci Web](#web-apis)
* [Testowalności](#testability)
* [Razor Wyświetl aparat](#razor-view-engine)
* [Widoki o jednoznacznie określonym typie](#strongly-typed-views)
* [Pomocnicy tagów](#tag-helpers)
* [Wyświetl składniki](#view-components)

### <a name="routing"></a>Routing

ASP.NET Core MVC jest oparty na [routingu ASP.NET Core](../fundamentals/routing.md), czyli zaawansowanym składniku mapowania adresów URL, który umożliwia tworzenie aplikacji, które mają zrozumiały i przeszukiwany adres URL. Pozwala to definiować wzorce nazewnictwa adresów URL aplikacji, które dobrze sprawdzają się w przypadku optymalizacji aparatu wyszukiwania (wyszukiwarki) i generowania linków, bez względu na sposób organizowania plików na serwerze sieci Web. Trasy można definiować przy użyciu wygodnej składni szablonu trasy, która obsługuje ograniczenia wartości tras, wartości domyślne i opcjonalne.

*Routing oparty na Konwencji* umożliwia globalne Definiowanie formatów adresów URL akceptowanych przez aplikację oraz sposobu mapowania poszczególnych formatów na określoną metodę akcji na danym kontrolerze. Po odebraniu żądania przychodzącego aparat routingu analizuje adres URL i dopasowuje go do jednego ze zdefiniowanych formatów adresów URL, a następnie wywołuje metodę akcji skojarzonego kontrolera.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Routing atrybutu* umożliwia określenie informacji o routingu przez dekorowania nazwy kontrolerów i akcji przy użyciu atrybutów, które definiują trasy aplikacji. Oznacza to, że definicje tras są umieszczane obok kontrolera i akcji, z którymi są skojarzone.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>Powiązanie modelu

ASP.NET Core [powiązanie modelu](models/model-binding.md) MVC konwertuje dane żądania klienta (wartości formularza, dane tras, parametry ciągu zapytania, nagłówki HTTP) na obiekty, które może obsłużyć kontroler. W związku z tym logika kontrolera nie musi wykonywać zadań, aby wyrównać dane żądania przychodzącego; po prostu zawiera dane jako parametry do metod akcji.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Walidacja modelu

ASP.NET Core MVC obsługuje [walidację](models/validation.md) przez dekorowania nazwy obiektu modelu z atrybutami walidacji adnotacji danych. Atrybuty walidacji są sprawdzane po stronie klienta, zanim wartości są ogłaszane na serwerze, a także na serwerze przed wywołaniem akcji kontrolera.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Akcja kontrolera:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

Platforma obsługuje walidację danych żądania zarówno na kliencie, jak i na serwerze. Logika walidacji określona w typach modeli jest dodawana do renderowanych widoków jako niedyskretne adnotacje i wymuszane w przeglądarce przy użyciu [walidacji jQuery](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Wstrzykiwanie zależności

ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](../fundamentals/dependency-injection.md). W ASP.NET Core MVC [Kontrolery](controllers/dependency-injection.md) mogą zażądać wymaganych usług za pomocą ich konstruktorów, umożliwiając im przestrzeganie [zasad jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

Twoja aplikacja może również używać [iniekcji zależności w plikach widoku](views/dependency-injection.md)przy użyciu `@inject` dyrektywy:

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtry

[Filtry](controllers/filters.md) ułatwiają deweloperom hermetyzację zagadnień związanych z zmniejszeniem, takich jak obsługa wyjątków czy autoryzacja. Filtry umożliwiają uruchamianie niestandardowej logiki sprzed i po przetworzeniu dla metod akcji i można ją skonfigurować do uruchamiania w określonych punktach w potoku wykonywania dla danego żądania. Filtry mogą być stosowane do kontrolerów lub akcji jako atrybuty (lub mogą być uruchamiane globalnie). W strukturze uwzględniono kilka filtrów (takich jak `Authorize` ). `[Authorize]` jest atrybutem używanym do tworzenia filtrów autoryzacji MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Obszary

[Obszary](controllers/areas.md) umożliwiają partycjonowanie dużej aplikacji sieci Web MVC ASP.NET Core w mniejszych grupach funkcjonalnych. Obszar jest strukturą MVC wewnątrz aplikacji. W projekcie MVC składniki logiczne, takie jak model, kontroler i widok, są przechowywane w różnych folderach, a MVC używają konwencji nazewnictwa, aby utworzyć relację między tymi składnikami. W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji. Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie, itp. Każda z tych jednostek ma własne widoki, kontrolery i modele logicznego składnika.

### <a name="web-apis"></a>Interfejsy API sieci Web

Poza doskonałym platformą do tworzenia witryn sieci Web, ASP.NET Core MVC ma doskonałą obsługę tworzenia interfejsów API sieci Web. Możesz tworzyć usługi, które docierają do szerokiego zakresu klientów, w tym przeglądarek i urządzeń przenośnych.

Platforma obejmuje obsługę negocjacji zawartości HTTP z wbudowaną obsługą [formatowania danych](xref:web-api/advanced/formatting) jako JSON lub XML. Napisz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters) , aby dodać obsługę własnych formatów.

Użyj generowania linków, aby włączyć obsługę multimediów. Łatwo Włącz obsługę [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) , aby interfejsy API sieci Web mogły być współużytkowane przez wiele aplikacji sieci Web.

### <a name="testability"></a>Testowalności

Korzystanie z interfejsów i iniekcja zależności umożliwia odpowiednie rozwiązanie do testowania jednostkowego, a platforma obejmuje funkcje (takie jak TestHost i Dostawca pamięci dla Entity Framework), które umożliwiają szybkie i łatwe testowanie [integracji](xref:test/integration-tests) . Dowiedz się więcej [na temat testowania logiki kontrolera](controllers/testing.md).

### <a name="no-locrazor-view-engine"></a>Razor Wyświetl aparat

[ASP.NET Core widoki MVC](views/overview.md) wykorzystują [ Razor aparat widoku](views/razor.md) do renderowania widoków. Razor to zwarty, wyraźny i płynny język znaczników szablonów do definiowania widoków przy użyciu osadzonego kodu w języku C#. Razor służy do dynamicznego generowania zawartości sieci Web na serwerze. Można wyczyścić kod serwera z zawartością i kodem po stronie klienta.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

Korzystając z Razor aparatu widoku, można definiować [układy](views/layout.md), [częściowe widoki](views/partial.md) i przemieścić sekcje.

### <a name="strongly-typed-views"></a>Widoki o jednoznacznie określonym typie

Razor widoki w MVC można jednoznacznie wpisywać na podstawie modelu. Kontrolery mogą przekazać silnie wpisany model do widoków, co umożliwia kontrolowanie typów i obsługę technologii IntelliSense.

Na przykład następujący widok renderuje model typu `IEnumerable<Product>` :

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Pomocnicy tagów

[Pomocnicy tagów](views/tag-helpers/intro.md) Włącz kod po stronie serwera, aby wziąć udział w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach. Za pomocą pomocników tagów można definiować niestandardowe znaczniki (na przykład `<environment>` ) lub modyfikować zachowanie istniejących tagów (na przykład `<label>` ). Pomocnicy tagów powiążą się z określonymi elementami na podstawie nazwy elementu i jego atrybutów. Zapewniają one zalety renderowania po stronie serwera, zachowując jednocześnie środowisko edycji HTML.

Istnieje wiele wbudowanych pomocników tagów dla typowych zadań, takich jak tworzenie formularzy, linków, ładowanie zasobów i inne — a nawet więcej dostępnych w publicznych repozytoriach GitHub i jako pakiety NuGet. Pomocnicy tagów są twórcy w języku C# i są elementami docelowymi elementów HTML w oparciu o nazwę elementu, nazwę atrybutu lub tag nadrzędny. Na przykład wbudowana LinkTagHelper może służyć do tworzenia linku do `Login` akcji `AccountsController` :

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper`Może służyć do uwzględnienia różnych skryptów w widokach (na przykład Raw lub zminimalizowanego) w oparciu o środowisko uruchomieniowe, takie jak programowanie, przemieszczanie lub produkcja:

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Pomocnicy tagów zapewniają przyjazne dla języka HTML środowisko programistyczne i zaawansowane środowisko IntelliSense do tworzenia kodu HTML i Razor znaczników. Większość wbudowanych pomocników tagów docelowo istniejące elementy HTML i udostępniają atrybuty po stronie serwera dla elementu.

### <a name="view-components"></a>Wyświetl składniki

[Składniki widoku](views/view-components.md) umożliwiają pakowanie logiki renderowania i ponowne używanie jej w całej aplikacji. Są one podobne do [widoków częściowych](views/partial.md), ale ze skojarzoną logiką.

## <a name="compatibility-version"></a>Wersja zgodności

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda pozwala aplikacji na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, wprowadzonych w ASP.NET Core MVC 2,1 lub nowszych.

Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [AspNetCore. MVC-Fluent — Biblioteka testów dla ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Biblioteka testów jednostkowych z jednoznacznie określonymi typami, dostarczająca interfejs Fluent do testowania aplikacji MVC i Web API. (*Niekonserwowane lub obsługiwane przez firmę Microsoft).*
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>
