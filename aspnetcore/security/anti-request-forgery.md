---
title: Uniemożliwiaj ataki między witrynami (XSRF/CSRF) w ASP.NET Core
author: steve-smith
description: Dowiedz się, jak zapobiegać atakom na aplikacje sieci Web, w których złośliwa witryna sieci Web może mieć wpływ na interakcję między przeglądarką klienta a aplikacją.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 0ec9aa5f2e1c9d439bbea2ac15ffb50b3e42daca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399338"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Uniemożliwiaj ataki między witrynami (XSRF/CSRF) w ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)i [Steve Smith](https://ardalis.com/)

Sfałszowanie żądań między lokacjami (znane również jako XSRF lub CSRF) jest atakiem na aplikacje hostowane w sieci Web, dzięki czemu złośliwa aplikacja internetowa może mieć wpływ na interakcję między przeglądarką klienta a aplikacją sieci Web, która ufa tej przeglądarce. Te ataki są możliwe, ponieważ przeglądarki sieci Web wysyłają automatyczne typy tokenów uwierzytelniania przy użyciu każdego żądania do witryny sieci Web. Ta forma wykorzystania jest również znana jako *atak dwukrotnego kliknięcia* lub *sesji* , ponieważ atakujący wykorzystuje wcześniej uwierzytelnioną sesję użytkownika.

Przykład ataku CSRF:

1. Użytkownik loguje się `www.good-banking-site.com` przy użyciu uwierzytelniania formularzy. Serwer uwierzytelnia użytkownika i wystawia odpowiedź obejmującą plik cookie uwierzytelniania. Lokacja jest narażona na ataki, ponieważ ufa każde żądanie odbierane z prawidłowym plikiem cookie uwierzytelniania.
1. Użytkownik odwiedza złośliwą lokację `www.bad-crook-site.com` .

   Złośliwa witryna programu `www.bad-crook-site.com` zawiera formularz HTML podobny do poniższego:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Zwróć uwagę na to, że formularz `action` wpisuje w nieszkodliwej witrynie. To jest część "wiele witryn" z CSRF.

1. Użytkownik wybiera przycisk Prześlij. Przeglądarka wysyła żądanie i automatycznie uwzględnia plik cookie uwierzytelniania dla żądanej domeny `www.good-banking-site.com` .
1. Żądanie jest uruchamiane na `www.good-banking-site.com` serwerze z kontekstem uwierzytelniania użytkownika i może wykonywać dowolną akcję, którą może wykonać uwierzytelniony użytkownik.

Oprócz scenariusza, w którym użytkownik wybiera przycisk, aby przesłać formularz, złośliwa witryna może:

* Uruchom skrypt, który automatycznie przesyła formularz.
* Wyślij formularz przesyłania w postaci żądania AJAX.
* Ukryj formularz przy użyciu CSS.

Te alternatywne scenariusze nie wymagają żadnych działań ani danych wejściowych od użytkownika innego niż początkowo odwiedzają złośliwe witryny.

Korzystanie z protokołu HTTPS nie uniemożliwia ataku CSRF. Złośliwa lokacja może wysłać `https://www.good-banking-site.com/` żądanie równie łatwo, jak może wysłać niezabezpieczone żądanie.

Niektóre ataki są ukierunkowanymi punktami końcowymi, które odpowiadają na żądania GET, w takim przypadku można użyć znacznika obrazu do wykonania akcji. Ta forma ataku jest wspólna w witrynach forum, które zezwalają na obrazy, ale blokują kod JavaScript. Aplikacje, które zmieniają stan w żądaniach GET, gdzie zmienne lub zasoby są zmieniane, są narażone na złośliwe ataki. **Żądania GET, które zmieniają stan są niezabezpieczone. Najlepszym rozwiązaniem jest nigdy nie zmieniaj stanu żądania GET.**

Możliwe jest ataki CSRF na aplikacje sieci Web, które używają plików cookie do uwierzytelniania, ponieważ:

* Przeglądarki przechowują pliki cookie wystawione przez aplikację internetową.
* Przechowywane pliki cookie obejmują pliki cookie sesji dla użytkowników uwierzytelnionych.
* Przeglądarki wysyłają do aplikacji sieci Web wszystkie pliki cookie skojarzone z domeną, niezależnie od tego, w jaki sposób żądanie do aplikacji zostało wygenerowane w przeglądarce.

Jednak ataki CSRF nie ograniczają się do korzystania z plików cookie. Na przykład uwierzytelnianie podstawowe i szyfrowane jest również zagrożone. Gdy użytkownik zaloguje się przy użyciu uwierzytelniania podstawowego lub szyfrowanego, przeglądarka automatycznie wyśle poświadczenia do momentu &dagger; zakończenia sesji.

&dagger;W tym kontekście *sesja* dotyczy sesji po stronie klienta, podczas której użytkownik jest uwierzytelniany. Nie jest on związany z sesjami po stronie serwera lub [ASP.NET Core pośredniczących sesji](xref:fundamentals/app-state).

Użytkownicy mogą chronić przed lukami w CSRF, podejmując środki ostrożności:

* Wyloguj się z aplikacji sieci Web po zakończeniu korzystania z nich.
* Okresowe czyszczenie plików cookie przeglądarki.

Jednak luki w zabezpieczeniach CSRF są zasadniczo problemem z aplikacją sieci Web, a nie użytkownikiem końcowym.

## <a name="authentication-fundamentals"></a>Podstawowe informacje uwierzytelniania

Uwierzytelnianie na podstawie plików cookie to popularna forma uwierzytelniania. Systemy uwierzytelniania oparte na tokenach zwiększają popularność, szczególnie w przypadku aplikacji jednostronicowych (aplikacji jednostronicowych).

### <a name="cookie-based-authentication"></a>Uwierzytelnianie na podstawie plików cookie

Gdy użytkownik jest uwierzytelniany przy użyciu nazwy użytkownika i hasła, zostanie wystawiony token zawierający bilet uwierzytelniania, który może być używany do uwierzytelniania i autoryzacji. Token jest przechowywany jako plik cookie, który jest dołączany do każdego żądania przez klienta. Generowanie i sprawdzanie poprawności tego pliku cookie jest wykonywane przez oprogramowanie pośredniczące uwierzytelniania plików cookie. [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index) deserializacji podmiotu zabezpieczeń użytkownika do zaszyfrowanego pliku cookie. W kolejnych żądaniach oprogramowanie pośredniczące weryfikuje plik cookie, ponownie tworzy podmiot zabezpieczeń i przypisuje podmiotowi zabezpieczeń do właściwości [użytkownika](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Uwierzytelnianie oparte na tokenach

Po uwierzytelnieniu użytkownika są wystawiane tokeny (nie token antysfałszowany). Token zawiera informacje o użytkowniku w formie [oświadczeń](/dotnet/framework/security/claims-based-identity-model) lub Token odwołania, który wskazuje, że stan użytkownika w aplikacji jest obsługiwany przez aplikację. Gdy użytkownik próbuje uzyskać dostęp do zasobu wymagającego uwierzytelnienia, token jest wysyłany do aplikacji z dodatkowym nagłówkiem autoryzacji w postaci tokenu okaziciela. Powoduje to bezstanową aplikację. W każdym kolejnym żądaniu token jest przesyłany do żądania weryfikacji po stronie serwera. Ten token nie jest *szyfrowany*; jest ona *zaszyfrowana*. Na serwerze token jest zdekodowany w celu uzyskania dostępu do informacji. Aby wysłać token w kolejnych żądaniach, Zapisz token w lokalnym magazynie przeglądarki. Nie należy obawiać się o luki w zabezpieczeniach CSRF, jeśli token jest przechowywany w lokalnym magazynie przeglądarki. CSRF jest problemem, gdy token jest przechowywany w pliku cookie. Aby uzyskać więcej informacji, zobacz przykładowy kod rozchód spa w usłudze GitHub [dodaje dwa pliki cookie](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Wiele aplikacji hostowanych w jednej domenie

Udostępnione środowiska hostingu są podatne na przejmowanie sesji, CSRF logowania i inne ataki.

Chociaż `example1.contoso.net` i `example2.contoso.net` są różnymi hostami, istnieje niejawna relacja zaufania między hostami w `*.contoso.net` domenie. Niejawne relacje zaufania umożliwiają potencjalnie niezaufanym hostom wpływanie na wszystkie inne pliki cookie (zasady tego samego źródła, które zarządza żądaniami AJAX, nie zawsze mają zastosowanie do plików cookie HTTP).

Ataki wykorzystujące zaufane pliki cookie między aplikacjami hostowanymi w tej samej domenie można zablokować, nie udostępniając domen. Gdy każda aplikacja jest hostowana we własnej domenie, nie istnieje niejawna relacja zaufania plików cookie do wykorzystania.

## <a name="aspnet-core-antiforgery-configuration"></a>Konfiguracja antysfałszowana ASP.NET Core

> [!WARNING]
> ASP.NET Core implementuje ochronę przed fałszowaniem za pomocą [ASP.NET Core ochrony danych](xref:security/data-protection/introduction). Stos ochrony danych musi być skonfigurowany do pracy w farmie serwerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ochrony danych](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy jeden z następujących interfejsów API jest wywoływany w `Startup.ConfigureServices` :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Oprogramowanie pośredniczące przed fałszowaniem jest dodawane do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) , gdy <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> jest wywoływana w`Startup.ConfigureServices`

::: moniker-end

W ASP.NET Core 2,0 lub nowszej [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) wprowadza do elementów formularza HTML tokeny zabezpieczające przed fałszerstwem. Następujące znaczniki w Razor pliku automatycznie generują tokeny zabezpieczające przed fałszerstwem:

```cshtml
<form method="post">
    ...
</form>
```

Podobnie [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) domyślnie generuje tokeny zabezpieczające przed fałszerstwem, jeśli nie można pobrać metody formularza.

Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML występuje, gdy `<form>` tag zawiera `method="post"` atrybut i jeden z następujących warunków jest spełniony:

* Atrybut Action jest pusty ( `action=""` ).
* Nie podano atrybutu Action ( `<form method="post">` ).

Automatyczne generowanie tokenów antysfałszowanych dla elementów formularza HTML może być wyłączone:

* Jawnie wyłącz tokeny zabezpieczające przed fałszowaniem z `asp-antiforgery` atrybutem:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Element form jest wyłączany z pomocników tagów przy użyciu [symbolu Autowypełniania tagów!](xref:mvc/views/tag-helpers/intro#opt-out)

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Usuń `FormTagHelper` z widoku. `FormTagHelper`Można usunąć z widoku, dodając do widoku następującą dyrektywę Razor :

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor Strony](xref:razor-pages/index) są automatycznie chronione przed XSRF/CSRF. Aby uzyskać więcej informacji, zobacz [XSRF/CSRF i Razor Pages](xref:razor-pages/index#xsrf).

Najbardziej typowym podejściem do obrony przed atakami CSRF jest użycie *wzorca tokenu synchronizatora* (STP). Wartość STP jest używana, gdy użytkownik żąda strony z danymi formularza:

1. Serwer wysyła do klienta token skojarzony z tożsamością bieżącego użytkownika.
1. Klient wysyła do serwera token z powrotem w celu weryfikacji.
1. Jeśli serwer odbiera token, który nie jest zgodny z tożsamością uwierzytelnionego użytkownika, żądanie zostanie odrzucone.

Token jest unikatowy i nieprzewidywalny. Token może również służyć do zapewnienia prawidłowej sekwencji serii żądań (na przykład w celu zapewnienia sekwencji żądań: Strona 1 > stronie 2 > stronie 3). Wszystkie formularze w ASP.NET Core MVC i Razor Szablony stron generują tokeny zabezpieczające przed fałszerstwem. Poniższa para przykładów widoku generuje tokeny zabezpieczające przed fałszerstwem:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Jawnie Dodaj token antysfałszowany do `<form>` elementu bez korzystania z pomocników tagów, korzystając z pomocy języka HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

W każdym z powyższych przypadków ASP.NET Core dodaje ukryte pole formularza podobne do następujących:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core zawiera trzy [filtry](xref:mvc/controllers/filters) do pracy z tokenami antyfałszowanymi:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Opcje samopodrabiania

Dostosuj [Opcje antysfałszowane](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) w programie `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Ustaw właściwości przeciwpodrabiane `Cookie` przy użyciu właściwości klasy [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Opcja | Opis |
| ------ | ----------- |
| [Plików](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Określa ustawienia używane do tworzenia plików cookie z fałszerstwem. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach. |
| [Nagłówek nagłówka](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nazwa nagłówka używanego przez system antysfałszowany. Jeśli `null` System uwzględnia tylko dane formularza. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka. Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN". Wartość domyślna to `false` . |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Opcja | Opis |
| ------ | ----------- |
| [Plików](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Określa ustawienia używane do tworzenia plików cookie z fałszerstwem. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Domena pliku cookie. Wartość domyślna to `null` . Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji. Zalecaną alternatywą jest plik cookie. domena. |
| [Plik CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nazwa pliku cookie. Jeśli nie zostanie ustawiona, system generuje unikatową nazwę rozpoczynającą się od [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. przed fałszerstwem "). Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji. Zalecaną alternatywą jest Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Ścieżka ustawiona w pliku cookie. Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji. Zalecaną alternatywą jest plik cookie. Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nazwa ukrytego pola formularza używanego przez system antysfałszowany do renderowania tokenów antysfałszowanych w widokach. |
| [Nagłówek nagłówka](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nazwa nagłówka używanego przez system antysfałszowany. Jeśli `null` System uwzględnia tylko dane formularza. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Określa, czy protokół HTTPS jest wymagany przez system antysfałszowany. Jeśli `true` żądania inne niż HTTPS kończą się niepowodzeniem. Wartość domyślna to `false` . Ta właściwość jest przestarzała i zostanie usunięta w przyszłej wersji. Zalecaną alternatywą jest ustawienie pliku cookie. SecurePolicy. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Określa, czy należy pominąć generowanie `X-Frame-Options` nagłówka. Domyślnie nagłówek jest generowany z wartością "SAMEORIGIN". Wartość domyślna to `false` . |

::: moniker-end

Aby uzyskać więcej informacji, zobacz [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Konfigurowanie funkcji antysfałszowanych za pomocą IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) udostępnia interfejs API do konfigurowania funkcji antysfałszowanych. `IAntiforgery`można żądać w `Configure` metodzie `Startup` klasy. W poniższym przykładzie użyto oprogramowania pośredniczącego ze strony głównej aplikacji do wygenerowania tokenu antysfałszowanego i wysłania go w odpowiedzi jako plik cookie (przy użyciu domyślnej konwencji nazewnictwa kątowego opisanej w dalszej części tego tematu):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Wymagaj weryfikacji przed fałszerstwem

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) to filtr akcji, który można zastosować do poszczególnych akcji, kontrolera lub globalnie. Żądania wykonane do akcji, do których zastosowano ten filtr są blokowane, chyba że żądanie zawiera prawidłowy token antysfałszowany.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

Ten `ValidateAntiForgeryToken` atrybut wymaga tokenu dla żądań do metod akcji, które to oznacza, w tym żądań HTTP GET. Jeśli `ValidateAntiForgeryToken` atrybut jest stosowany w kontrolerach aplikacji, można go zastąpić `IgnoreAntiforgeryToken` atrybutem.

> [!NOTE]
> ASP.NET Core nie obsługuje dodawania tokenów antysfałszowanych w celu automatycznego uzyskiwania żądań.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Automatycznie Weryfikuj tokeny antysfałszowane wyłącznie dla niebezpiecznych metod HTTP

Aplikacje ASP.NET Core nie generują tokenów antysfałszowanych dla bezpiecznych metod HTTP (GET, głowy, OPTIONS i TRACE). Zamiast szeroko stosowanego `ValidateAntiForgeryToken` atrybutu, a następnie zastępując go atrybutami `IgnoreAntiforgeryToken` , można użyć atrybutu [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) . Ten atrybut działa identycznie z `ValidateAntiForgeryToken` atrybutem, z tą różnicą, że nie wymaga tokenów dla żądań wysyłanych przy użyciu następujących metod http:

* GET
* HEAD
* Opcje
* TRACE

Zalecamy użycie `AutoValidateAntiforgeryToken` ogólnie dla scenariuszy innych niż interfejsy API. Gwarantuje to, że akcje wykonywane domyślnie są chronione. Alternatywą jest ignorowanie tokenów antysfałszowanych domyślnie, chyba że `ValidateAntiForgeryToken` jest on stosowany do poszczególnych metod akcji. Bardziej prawdopodobnie w tym scenariuszu, aby metoda po akcji nie była chroniona przez pomyłkę, pozostawiając aplikację narażoną na ataki CSRF. Wszystkie wpisy powinny wysyłać token antysfałszowany.

Interfejsy API nie mają mechanizmu automatycznego do wysyłania niezwiązanego z plikiem cookie części tokenu. Implementacja prawdopodobnie zależy od implementacji kodu klienta. Poniżej przedstawiono kilka przykładów:

Przykład na poziomie klasy:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Przykład globalny:

::: moniker range="< aspnetcore-3.0"

Services. AddMvc (opcje => opcje. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Zastąp atrybuty globalnym lub kontrolerem antyfałszowanym

Filtr [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) służy do eliminacji potrzeb tokenu antysfałszowanego dla danej akcji (lub kontrolera). Po zastosowaniu ten filtr przesłania `ValidateAntiForgeryToken` i `AutoValidateAntiforgeryToken` filtry określone na wyższym poziomie (globalnie lub na kontrolerze).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Odśwież tokeny po uwierzytelnieniu

Tokeny należy odświeżyć po uwierzytelnieniu użytkownika przez przekierowanie użytkownika do strony widoku lub strony Razor .

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX i aplikacji jednostronicowych

W tradycyjnych aplikacjach opartych na języku HTML tokeny zabezpieczające są przesyłane do serwera przy użyciu ukrytych pól formularzy. W nowoczesnych aplikacjach opartych na języku JavaScript i aplikacji jednostronicowych wiele żądań jest wykonywanych programowo. Te żądania AJAX mogą używać innych technik (takich jak nagłówki żądań lub pliki cookie) do wysyłania tokenu.

Jeśli pliki cookie są używane do przechowywania tokenów uwierzytelniania i uwierzytelniania żądań interfejsu API na serwerze, CSRF jest potencjalnym problemem. Jeśli magazyn lokalny jest używany do przechowywania tokenu, luka w zabezpieczeniach CSRF może zostać wyeliminowana, ponieważ wartości z magazynu lokalnego nie są automatycznie wysyłane do serwera przy użyciu każdego żądania. W tym celu należy użyć magazynu lokalnego do przechowywania na kliencie tokenu antysfałszowanego i wysyłania tokenu jako nagłówka żądania.

### <a name="javascript"></a>JavaScript

Przy użyciu języka JavaScript z widokami token można utworzyć przy użyciu usługi z poziomu widoku. Wsuń usługę [Microsoft. AspNetCore. antyfałszerstwe. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) do widoku i Wywołaj [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Takie podejście eliminuje konieczność bezpośredniej obsługi ustawień plików cookie z serwera lub odczytywanie ich z klienta programu.

Poprzedni przykład używa języka JavaScript do odczytywania wartości pola ukrytego dla nagłówka z WPISem AJAX.

Język JavaScript może również uzyskiwać dostęp do tokenów w plikach cookie i używać zawartości pliku cookie do tworzenia nagłówka z wartością tokenu.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Zakładając, że żądanie skryptu wysłano token w nagłówku o nazwie `X-CSRF-TOKEN` , skonfiguruj usługę antysfałszowaną, aby wyszukać `X-CSRF-TOKEN` Nagłówek:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Poniższy przykład używa języka JavaScript, aby wykonać żądanie AJAX z odpowiednim nagłówkiem:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS używa konwencji do adresowania CSRF. Jeśli serwer wysyła plik cookie o nazwie `XSRF-TOKEN` , `$http` Usługa AngularJS dodaje wartość cookie do nagłówka podczas wysyłania żądania do serwera. Ten proces jest automatyczny. Nagłówek nie musi być jawnie ustawiony na kliencie. Nazwa nagłówka to `X-XSRF-TOKEN` . Serwer powinien wykryć ten nagłówek i zweryfikować jego zawartość.

Aby program ASP.NET Core API działał z tą konwencją podczas uruchamiania aplikacji:

* Skonfiguruj aplikację, aby zapewnić token w pliku cookie o nazwie `XSRF-TOKEN` .
* Skonfiguruj usługę antysfałszowaną, aby wyszukać nagłówek o nazwie `X-XSRF-TOKEN` .

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Zwiększ fałszerstwo

Typ [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) umożliwia deweloperom zwiększenie zachowania systemu antyCSRFowego przez dwukrotne wyzwolenie dodatkowych danych w każdym tokenie. Metoda [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) jest wywoływana za każdym razem, gdy generowany jest token pola, a zwracana wartość jest osadzona w wygenerowanym tokenie. Realizator może zwrócić sygnaturę czasową, identyfikator jednorazowy lub inną wartość, a następnie wywołać [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) , aby zweryfikować te dane po sprawdzeniu poprawności tokenu. Nazwa użytkownika klienta jest już osadzona w wygenerowanych tokenach, więc nie trzeba uwzględniać tych informacji. Jeśli token zawiera dane uzupełniające, ale nie `IAntiForgeryAdditionalDataProvider` jest skonfigurowany, dane uzupełniające nie są sprawdzane.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) w [programie Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
