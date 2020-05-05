---
title: ASP.NET Core Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, Razor jak udostępnić składniki użytkownikom w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772494"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>ASP.NET Core Blazor globalizacja i lokalizacja

Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)

Razorskładniki mogą być udostępniane użytkownikom w wielu kulturach i językach. Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:

* . System zasobów netto
* Formatowanie liczb i dat specyficznych dla kultury

Obecnie obsługiwane są ograniczone zestawy ASP.NET Core scenariuszy lokalizacji:

* `IStringLocalizer<>`*jest obsługiwany* w Blazor aplikacjach.
* `IHtmlLocalizer<>`Lokalizacje adnotacji danych są ASP.NET Core w scenariuszach MVC i **nie są obsługiwane** w Blazor aplikacjach. `IViewLocalizer<>`

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizacja

Blazor`@bind` funkcja wykonuje formatowanie i analizuje wartości na potrzeby wyświetlania w oparciu o bieżącą kulturę użytkownika.

Dla bieżącej kultury można uzyskać dostęp z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> właściwości.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) jest używany dla następujących typów pól (`<input type="{TYPE}" />`):

* `date`
* `number`

Poprzednie typy pól:

* Są wyświetlane przy użyciu odpowiednich reguł formatowania opartych na przeglądarce.
* Nie może zawierać tekstu o dowolnym formacie.
* Podaj charakterystykę interakcji użytkownika w oparciu o implementację przeglądarki.

Następujące typy pól mają określone wymagania dotyczące formatowania i nie są obecnie obsługiwane Blazor przez program, ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:

* `datetime-local`
* `month`
* `week`

`@bind`obsługuje `@bind:culture` parametr, aby zapewnić <xref:System.Globalization.CultureInfo?displayProperty=fullName> analizę i formatowanie wartości. Określanie kultury nie jest zalecane w `date` przypadku używania typów `number` pól i. `date`i `number` ma wbudowaną Blazor obsługę, która udostępnia wymaganą kulturę.

## <a name="localization"></a>Lokalizacja

### <a name="blazor-webassembly"></a>BlazorZestaw webassembly

BlazorAplikacje webassembly ustawiają kulturę przy użyciu [preferencji języka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)użytkownika.

Aby jawnie skonfigurować kulturę, ustaw `CultureInfo.DefaultThreadCurrentCulture` i `CultureInfo.DefaultThreadCurrentUICulture` w `Program.Main`.

Domyślnie Blazorkonfiguracja konsolidatora dla aplikacji webassembly umożliwia Blazor rozłączenie informacji o danych wielojęzycznych z wyjątkiem lokalizacji lokalnych jawnie żądanych. Aby uzyskać więcej informacji i wskazówek dotyczących kontrolowania zachowania konsolidatora, zobacz <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

Chociaż kultura, która Blazor wybiera domyślnie, może być wystarczająca dla większości użytkowników, należy rozważyć umożliwienie użytkownikom określenia ich preferowanych ustawień regionalnych. Blazor Aby zapoznać się z przykładową aplikacją webassembly z selektorem kultury, zobacz przykładową aplikację [LocSample](https://github.com/pranavkm/LocSample) lokalizacyjną.

### <a name="blazor-server"></a>BlazorServer

BlazorAplikacje serwera są zlokalizowane przy użyciu [oprogramowania pośredniczącego](xref:fundamentals/localization#localization-middleware). Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.

Kulturę można ustawić przy użyciu jednej z następujących metod:

* [Plik cookie](#cookies)
* [Podaj interfejs użytkownika, aby wybrać kulturę](#provide-ui-to-choose-the-culture)

Aby uzyskać więcej informacji i przykładów, <xref:fundamentals/localization>Zobacz.

#### <a name="cookies"></a>Pliki cookie

Plik cookie kultury lokalizacji może utrzymywać kulturę użytkownika. Plik cookie jest tworzony przez `OnGet` metodę strony hosta aplikacji (*strony/host. cshtml. cs*). Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie na kolejnych żądaniach, aby ustawić kulturę użytkownika. 

Użycie pliku cookie zapewnia, że połączenie z użyciem protokołu WebSocket może prawidłowo propagować kulturę. Jeśli schematy lokalizacji są oparte na ścieżce URL lub ciągu zapytania, schemat może nie być w stanie współdziałać z usługą WebSockets, więc nie będzie można zachować kultury. W związku z tym zalecanym podejściem jest użycie pliku cookie kultury lokalizacji.

Każda technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w pliku cookie lokalizacji. Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, Kontynuuj korzystanie z istniejącej infrastruktury lokalizacji aplikacji i Ustaw plik cookie kultury lokalizacji w schemacie aplikacji.

Poniższy przykład pokazuje, jak ustawić bieżącą kulturę w pliku cookie, który może zostać odczytany przez oprogramowanie pośredniczące lokalizacji. Utwórz plik *Pages/_Host. cshtml. cs* z następującą zawartością w aplikacji Blazor serwerowej:

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

Lokalizacja jest obsługiwana przez aplikację w następującej kolejności zdarzeń:

1. Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.
1. Kultura jest przypisana przez oprogramowanie pośredniczące lokalizacji.
1. `OnGet` Metoda w *_Host. cshtml. cs* utrzymuje kulturę w pliku cookie jako część odpowiedzi.
1. Przeglądarka otwiera połączenie WebSocket, aby utworzyć interaktywną Blazor sesję serwera.
1. Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie i przypisuje kulturę.
1. Sesja Blazor serwera rozpoczyna się od poprawnej kultury.

#### <a name="provide-ui-to-choose-the-culture"></a>Podaj interfejs użytkownika, aby wybrać kulturę

Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, zalecane jest *podejście oparte na przekierowaniu* . Ten proces jest podobny do tego, co się dzieje w aplikacji sieci Web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu. Użytkownik zostanie przekierowany do strony logowania, a następnie przekierowany z powrotem do oryginalnego zasobu. 

Aplikacja utrzymuje wybraną kulturę użytkownika za pośrednictwem przekierowania do kontrolera. Kontroler ustawia wybraną kulturę użytkownika na plik cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.

Ustanów punkt końcowy HTTP na serwerze, aby ustawić kulturę wybraną przez użytkownika w pliku cookie i wykonać przekierowanie z powrotem do oryginalnego identyfikatora URI:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Użyj wyniku `LocalRedirect` działania, aby zapobiec atakom typu Open redirect. Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.

Poniższy składnik przedstawia przykład sposobu wykonywania wstępnego przekierowania, gdy użytkownik wybierze kulturę:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/localization>
