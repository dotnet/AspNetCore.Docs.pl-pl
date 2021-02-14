---
title: ASP.NET Core Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, jak udostępnić Razor składniki użytkownikom w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 8baee9bc0a8e569174f33dac6a406b2162d09552
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279910"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>ASP.NET Core Blazor globalizacja i lokalizacja

Razor składniki mogą być udostępniane użytkownikom w wielu kulturach i językach. Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:

* . System zasobów netto
* Formatowanie liczb i dat specyficznych dla kultury

Obecnie obsługiwane są ograniczone zestawy ASP.NET Core scenariuszy lokalizacji:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> i <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *są obsługiwane* w Blazor aplikacjach.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer><xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>lokalizacje adnotacji danych są ASP.NET Core w SCENARIUSZACH MVC i **nie są obsługiwane** w Blazor aplikacjach.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizacja

Blazor[`@bind`](xref:mvc/views/razor#bind)Funkcja wykonuje formatowanie i analizuje wartości na potrzeby wyświetlania w oparciu o bieżącą kulturę użytkownika.

Dla bieżącej kultury można uzyskać dostęp z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> właściwości.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> jest używany dla następujących typów pól ( `<input type="{TYPE}" />` ):

* `date`
* `number`

Poprzednie typy pól:

* Są wyświetlane przy użyciu odpowiednich reguł formatowania opartych na przeglądarce.
* Nie może zawierać tekstu o dowolnym formacie.
* Podaj charakterystykę interakcji użytkownika w oparciu o implementację przeglądarki.

Następujące typy pól mają określone wymagania dotyczące formatowania i nie są obecnie obsługiwane przez program, Blazor ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) obsługuje `@bind:culture` parametr, aby zapewnić <xref:System.Globalization.CultureInfo?displayProperty=fullName> analizę i formatowanie wartości. Określanie kultury nie jest zalecane w przypadku używania `date` `number` typów pól i. `date` i `number` ma wbudowaną Blazor obsługę, która udostępnia wymaganą kulturę.

## <a name="localization"></a>Lokalizacja

### Blazor WebAssembly

Blazor WebAssembly aplikacje ustawiają kulturę przy użyciu [preferencji języka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)użytkownika.

Aby jawnie skonfigurować kulturę, ustaw <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> i <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> w `Program.Main` .

::: moniker range=">= aspnetcore-5.0"

Domyślnie program Blazor WebAssembly przenosi minimalne zasoby globalizacji wymagane do wyświetlania wartości, takich jak daty i waluta, w kulturze użytkownika. Aplikacje, które muszą obsługiwać dynamiczną zmianę kultury, powinny być konfigurowane `BlazorWebAssemblyLoadAllGlobalizationData` w pliku projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Blazor WebAssembly można również skonfigurować do uruchamiania przy użyciu określonej kultury aplikacji przy użyciu opcji przekazaną do `Blazor.start` . Przykładowo poniżej przedstawiono aplikację skonfigurowaną do uruchamiania przy użyciu `en-GB` kultury:

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

Wartość parametru `applicationCulture` musi być zgodna z [formatem taga języka BCP-47](https://tools.ietf.org/html/bcp47).

Jeśli aplikacja nie wymaga lokalizacji, można skonfigurować aplikację do obsługi niezmiennej kultury, która jest oparta na `en-US` kulturze:

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Domyślnie konfiguracja konsolidatora języka pośredniego (IL) dla aplikacji umożliwia rozłączenie Blazor WebAssembly informacji o ustawieniach regionalnych, z wyjątkiem sytuacji, w których jawnie zażądano. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

Chociaż kultura, która Blazor wybiera domyślnie, może być wystarczająca dla większości użytkowników, należy rozważyć umożliwienie użytkownikom określenia ich preferowanych ustawień regionalnych. Aby uzyskać Blazor WebAssembly przykładową aplikację z selektorem kultury, zobacz [`LocSample`](https://github.com/pranavkm/LocSample) przykładową aplikację lokalizacyjną.

### Blazor Server

Blazor Server aplikacje są zlokalizowane przy użyciu [oprogramowania pośredniczącego](xref:fundamentals/localization#localization-middleware). Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.

Kulturę można ustawić przy użyciu jednej z następujących metod:

* [Cookiewolumin](#cookies)
* [Podaj interfejs użytkownika, aby wybrać kulturę](#provide-ui-to-choose-the-culture)

Aby uzyskać więcej informacji i przykładów, zobacz <xref:fundamentals/localization> .

#### <a name="cookies"></a>Cookiewolumin

Kultura lokalizacji cookie może utrwalać kulturę użytkownika. Oprogramowanie pośredniczące lokalizacji odczytuje cookie kolejne żądania, aby ustawić kulturę użytkownika. 

Użycie a cookie zapewnia, że połączenie WebSocket może prawidłowo propagować kulturę. Jeśli schematy lokalizacji są oparte na ścieżce URL lub ciągu zapytania, schemat może nie być w stanie współdziałać z usługą WebSockets, więc nie będzie można zachować kultury. W związku z tym zaleca się użycie kultury lokalizacji cookie .

Każda technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w lokalizacji cookie . Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, Kontynuuj korzystanie z istniejącej infrastruktury lokalizacji aplikacji i ustaw kulturę lokalizacji cookie w schemacie aplikacji.

Poniższy przykład pokazuje, jak ustawić bieżącą kulturę cookie , która może być odczytana przez oprogramowanie pośredniczące lokalizacji. Utwórz Razor wyrażenie w `Pages/_Host.cshtml` pliku bezpośrednio wewnątrz tagu otwierającego `<body>` :

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

Lokalizacja jest obsługiwana przez aplikację w następującej kolejności zdarzeń:

1. Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.
1. Kultura jest przypisana przez oprogramowanie pośredniczące lokalizacji.
1. RazorWyrażenie na `_Host` stronie ( `_Host.cshtml` ) utrwala kulturę w cookie jako część odpowiedzi.
1. Przeglądarka otwiera połączenie WebSocket, aby utworzyć sesję interaktywną Blazor Server .
1. Oprogramowanie pośredniczące lokalizacji odczytuje cookie i przypisuje kulturę.
1. Blazor ServerSesja rozpoczyna się od poprawnej kultury.

Podczas pracy z <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage> , użyj <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> Właściwości:

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a>Podaj interfejs użytkownika, aby wybrać kulturę

Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, zalecane jest *podejście oparte na przekierowaniu* . Ten proces jest podobny do tego, co się dzieje w aplikacji sieci Web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu. Użytkownik zostanie przekierowany do strony logowania, a następnie przekierowany z powrotem do oryginalnego zasobu. 

Aplikacja utrzymuje wybraną kulturę użytkownika za pośrednictwem przekierowania do kontrolera. Kontroler ustawia wybraną kulturę użytkownika na cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.

Ustanów punkt końcowy HTTP na serwerze, aby ustawić kulturę wybraną przez użytkownika cookie i przekierować z powrotem do oryginalnego identyfikatora URI:

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
> Użyj <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> wyniku działania, aby zapobiec atakom typu Open redirect. Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.

Jeśli aplikacja nie jest skonfigurowana do przetwarzania akcji kontrolera:

* Dodaj usługi MVC do kolekcji usług w `Startup.ConfigureServices` :

  ```csharp
  services.AddControllers();
  ```

* Dodaj Routing punktu końcowego kontrolera w `Startup.Configure` :

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/localization>
