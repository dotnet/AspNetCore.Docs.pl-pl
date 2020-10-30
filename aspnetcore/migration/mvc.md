---
title: Dowiedz się, jak przeprowadzić migrację z ASP.NET MVC do ASP.NET Core MVC
author: wadepickett
description: Dowiedz się, jak rozpocząć Migrowanie projektu ASP.NET MVC do ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051516"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrowanie z ASP.NET MVC do ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview). W procesie są wyróżniane powiązane zmiany z ASP.NET MVC.

Migrowanie z ASP.NET MVC jest procesem wieloetapowym. W tym artykule omówiono następujące zagadnienia:

* Początkowa konfiguracja.
* Podstawowe kontrolery i widoki.
* Zawartość statyczna.
* Zależności po stronie klienta.

Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Tworzenie projektu Starter ASP.NET MVC

Utwórz przykładowy projekt ASP.NET MVC w programie Visual Studio do migracji:

1. Z menu **plik** wybierz pozycję **Nowy** > **projekt** .
1. Wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)** , a następnie wybierz pozycję **dalej** .
1. Nazwij projekt *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku. Wybierz pozycję **Utwórz** .
1. Wybierz pozycję **MVC** , a następnie wybierz pozycję **Utwórz** .

## <a name="create-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nowe rozwiązanie z nowym projektem ASP.NET Core, aby przeprowadzić migrację do:

1. Uruchom drugie wystąpienie programu Visual Studio.
1. Z menu **plik** wybierz pozycję **Nowy** > **projekt** .
1. Wybierz **ASP.NET Core aplikacji sieci Web** , a następnie wybierz przycisk **dalej** .
1. W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *WebApp1* .
1. Ustaw lokalizację na inny katalog niż poprzedni projekt, aby użyć tej samej nazwy projektu. Korzystanie z tej samej przestrzeni nazw ułatwia kopiowanie kodu między dwoma projektami. Wybierz pozycję **Utwórz** .
1. W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** . Wybierz szablon projektu **aplikacja sieci Web (Model-View-Controller)** i wybierz pozycję **Utwórz** .

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Skonfiguruj witrynę ASP.NET Core, aby używać MVC

W ASP.NET Core 3,0 i nowszych projektach .NET Framework nie jest już obsługiwaną platformą docelową. Projekt musi być przeznaczony dla platformy .NET Core. ASP.NET Core Shared Framework, która obejmuje MVC, jest częścią instalacji środowiska uruchomieniowego platformy .NET Core. W przypadku korzystania z `Microsoft.NET.Sdk.Web` zestawu SDK w pliku projektu automatycznie występuje odwołanie do udostępnionej struktury:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Aby uzyskać więcej informacji, zobacz temat [informacje dotyczące platformy](xref:migration/22-to-30#framework-reference).

W ASP.NET Core `Startup` Klasa:

* Zastępuje *Global. asax* .
* Obsługuje wszystkie zadania uruchamiania aplikacji.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

W projekcie ASP.NET Core Otwórz plik *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

Aplikacje ASP.NET Core muszą przystąpić do struktur funkcji za pomocą oprogramowania pośredniczącego. Poprzedni kod wygenerowany przez szablon dodaje następujące usługi i oprogramowanie pośredniczące:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Metoda rozszerzenia rejestruje obsługę usług MVC dla kontrolerów, funkcji związanych z interfejsem API i widoków. Aby uzyskać więcej informacji na temat opcji rejestracji usługi MVC, zobacz temat [Rejestrowanie usługi MVC](xref:migration/22-to-30#mvc-service-registration)
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`Metoda rozszerzenia musi być wywoływana przed `UseRouting` . Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Metoda rozszerzenia dodaje Routing. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

Ta istniejąca konfiguracja zawiera informacje potrzebne do migracji przykładowego projektu MVC ASP.NET. Aby uzyskać więcej informacji o ASP.NET Core opcjach oprogramowania pośredniczącego, zobacz <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrowanie kontrolerów i widoków

W ASP.NET Core projekcie zostanie dodana nowa pusta Klasa kontrolera i Klasa widoku, które będą służyć jako symbole zastępcze przy użyciu takich samych nazw, jak kontroler i klasy widoków w dowolnym projekcie ASP.NET MVC do migracji.

Projekt ASP.NET Core *WebApp1* zawiera już minimalny przykładowy kontroler i widok o takiej samej nazwie jak projekt ASP.NET MVC. Dzięki temu będą one służyć jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków do migracji z projektu ASP.NET MVC *WebApp1* .

1. Skopiuj metody z ASP.NET MVC, `HomeController` Aby zastąpić nowe `HomeController` metody ASP.NET Core. Nie trzeba zmieniać zwracanego typu metod akcji. Zwracany typ metody akcji kontrolera wbudowanego szablonu MVC ASP.NET to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` . `ActionResult` implementuje `IActionResult` .
1. W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *widok/katalog macierzysty* , wybierz polecenie **Dodaj** > **istniejący element** .
1. W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/katalogu macierzystego* projektu ASP.NET MVC *WebApp1* .
1. Zaznacz pliki widoku *Informacje o. cshtml* , *Contact. cshtml* i *index. cshtml* Razor , a następnie wybierz pozycję **Dodaj** , zastępując istniejące pliki.

Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/actions> i <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Przetestuj każdą metodę

Każdy punkt końcowy kontrolera może być testowany, ale układ i style są omówione w dalszej części dokumentu.

1. Uruchom aplikację ASP.NET Core.
1. Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w ASP.NET Core projekcie. Na przykład `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrowanie zawartości statycznej

W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z katalogu głównego projektu sieci Web i była mieszana przy użyciu plików po stronie serwera. W ASP.NET Core pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu. Domyślnym katalogiem jest *{Content root}/wwwroot* , ale można go zmienić. Aby uzyskać więcej informacji, zobacz [pliki statyczne w ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Skopiuj zawartość statyczną z projektu ASP.NET MVC *WebApp1* do katalogu *wwwroot* w projekcie ASP.NET Core *WebApp1* :

1. W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *wwwroot* , wybierz pozycję **Dodaj** > **istniejący element** .
1. W oknie dialogowym **Dodaj istniejący element** przejdź do projektu ASP.NET MVC *WebApp1* .
1. Wybierz plik *favicon. ico* , a następnie wybierz pozycję **Dodaj** , zastępując istniejący plik.

## <a name="migrate-the-layout-files"></a>Migrowanie plików układu

Skopiuj pliki układu projektu MVC ASP.NET do projektu ASP.NET Core:

1. W projekcie ASP.NET Core kliknij prawym przyciskiem myszy katalog *widoki* , a następnie wybierz pozycję **Dodaj** > **istniejący element** .
1. W oknie dialogowym **Dodaj istniejący element** przejdź do katalogu *widoków* projektu ASP.NET MVC *WebApp1* .
1. Wybierz plik *_ViewStart. cshtml* , a następnie wybierz pozycję **Dodaj** .

Skopiuj pliki wspólnych układów projektu ASP.NET MVC do projektu ASP.NET Core:

1. W projekcie ASP.NET Core kliknij prawym przyciskiem myszy *Widok/udostępniony* katalog, wybierz polecenie **Dodaj** > **istniejący element** .
1. W oknie dialogowym **Dodaj istniejący element** przejdź do *widoku/udostępnionego* katalogu projektu ASP.NET MVC *WebApp1* .
1. Wybierz plik *_Layout. cshtml* , a następnie wybierz pozycję **Dodaj** , zastępując istniejący plik.

W projekcie ASP.NET Core Otwórz plik *_Layout. cshtml* . Wprowadź następujące zmiany, aby dopasować do pokazanego poniżej kodu:

Zaktualizuj dołączenie do początkowego kodu CSS, aby pasowało do pożądanego poniższego szablonu:

1. Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).
1. Usuń `@Scripts.Render("~/bundles/modernizr")` .

Zakończony znacznik zastępczy dla dołączania do ładowania początkowego:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Zaktualizuj dołączenie jQuery i Bootstrap w języku JavaScript, aby dopasować do poniższego kodu:

1. Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).
1. Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).

Zakończono dołączenie znacznika wymiany dla platformy jQuery i uruchomienia skryptu uruchomieniowego w języku JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Wyświetl witrynę w przeglądarce. Powinien on być renderowany przy użyciu oczekiwanych stylów.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie grupowania i minifikacja

ASP.NET Core jest zgodny z kilkoma minifikacjaami i rozwiązaniami typu open source, takimi jak [Weboptimize](https://github.com/ligershark/WebOptimizer) i innymi podobnymi bibliotekami. ASP.NET Core nie zapewnia natywnego tworzenia i minifikacja rozwiązania. Aby uzyskać informacje na temat konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Rozwiązywanie błędów HTTP 500

Istnieje wiele problemów, które mogą spowodować wyświetlenie komunikatu o błędzie HTTP 500, który nie zawiera żadnych informacji na temat źródła problemu. Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500. Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane, gdy środowisko jest *opracowywane* . Jest to szczegółowo opisany w poniższym kodzie:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500. Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze. Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Następne kroki

* <xref:migration/identity>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,2. W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC. Migrowanie z ASP.NET MVC jest procesem wieloetapowym. W tym artykule omówiono następujące zagadnienia:

* Początkowa konfiguracja
* Podstawowe kontrolery i widoki
* Zawartość statyczna
* Zależności po stronie klienta.

Aby migrować konfigurację i Identity kod, zobacz <xref:migration/configuration> i <xref:migration/identity> .

> [!NOTE]
> Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.

## <a name="create-the-starter-aspnet-mvc-project"></a>Tworzenie projektu Starter ASP.NET MVC

Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC. Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5* . Program Visual Studio wyświetla nową nazwę rozwiązania ( *Mvc5* ), która ułatwia poinformowanie tego projektu z następnego projektu.

## <a name="create-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie ( *WebApp1* ), tak aby przestrzenie nazw w obu projektach były zgodne. Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami. Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* . Nazwij projekt *WebApp1* i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników** . Zmień nazwę tej aplikacji na *FullAspNetCore* . Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji. Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurowanie lokacji do korzystania z MVC

* W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC. W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.

`Microsoft.AspNetCore.Mvc` jest platformą ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles` jest programem obsługi plików statycznych. Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych. Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).

* Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego. Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Dodawanie kontrolera i widoku

W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.

* Dodaj katalog *kontrolerów* .

* Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* Dodaj katalog *widoków* .

* Dodaj *widok/katalog macierzysty* .

* Dodaj **Razor Widok** o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

Poniżej przedstawiono strukturę projektu:

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:

```html
<h1>Hello world!</h1>
```

Uruchom aplikację.

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).

Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:

* zawartość po stronie klienta (CSS, Fonts i scripts)

* kontrolery

* widoki

* modele

* tworzenia pakietów

* filtry

* Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).

## <a name="controllers-and-views"></a>Kontrolery i widoki

* Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` . W ASP.NET MVC typ zwracany metody akcji kontrolera wbudowanego szablonu to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` . `ActionResult` implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.

* Skopiuj pliki *. cshtml* , *Contact. cshtml* i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.

## <a name="test-each-method"></a>Przetestuj każdą metodę

Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku. Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.

Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core. Na przykład: `https://localhost:44375/home/about`.

![Strona kontaktu](mvc/_static/contact-page.png)

Zwróć uwagę na brak stylów i elementów menu. Styl zostanie rozwiązany w następnej sekcji.

## <a name="static-content"></a>Zawartość statyczna

W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera. W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* . Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core. W tej przykładowej konwersji:

* Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.

Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* . Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu ( *views/Shared/_Layout. cshtml* ). Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie. Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.

## <a name="migrate-the-layout-file"></a>Migrowanie pliku układu

* Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core. Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.

* Utwórz *widok/katalog udostępniony* .

* *Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core. Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* . Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro). Pomocnicy tagów są używani w nowym pliku układu. Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.

* Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.

Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):

* Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).

* Usuń `@Scripts.Render("~/bundles/modernizr")` .

* Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ). Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)

* Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).

* Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).

Znacznik zastępczy dla dołączania do ładowania początkowego:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Wyświetl witrynę w przeglądarce. Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.

* *Opcjonalne:* Spróbuj użyć nowego pliku układu. Skopiuj plik układu z projektu *FullAspNetCore* . Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie grupowania i minifikacja

Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Rozwiązywanie błędów HTTP 500

Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu. Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500. Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania* konfiguracji. Zobacz przykład w poniższym kodzie:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500. Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze. Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](xref:mvc/overview) 2,1. W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC. Migrowanie z ASP.NET MVC jest procesem wieloetapowym. W tym artykule omówiono następujące zagadnienia:

* Początkowa konfiguracja
* Podstawowe kontrolery i widoki
* Zawartość statyczna
* Zależności po stronie klienta.

Aby przeprowadzić migrację konfiguracji i Identity kodu, zobacz [Migrowanie konfiguracji do ASP.NET Core](xref:migration/configuration) i [migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity).

> [!NOTE]
> Numery wersji w przykładach mogą nie być aktualne, należy odpowiednio zaktualizować projekty.

## <a name="create-the-starter-aspnet-mvc-project"></a>Tworzenie projektu Starter ASP.NET MVC

Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC. Utwórz go przy użyciu nazwy *WebApp1* , aby przestrzeń nazw była zgodna z projektem ASP.NET Core utworzonym w następnym kroku.

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5* . Program Visual Studio wyświetla nową nazwę rozwiązania ( *Mvc5* ), która ułatwia poinformowanie tego projektu z następnego projektu.

## <a name="create-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie ( *WebApp1* ), tak aby przestrzenie nazw w obu projektach były zgodne. Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami. Utwórz ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* . Nazwij projekt *WebApp1* i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników** . Zmień nazwę tej aplikacji na *FullAspNetCore* . Utworzenie tego projektu powoduje zaoszczędzenie czasu w konwersji. Wynik końcowy może być wyświetlany w kodzie generowanym przez szablon, kod może być kopiowany do projektu konwersji lub porównywany z projektem generowanym przez szablon.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurowanie lokacji do korzystania z MVC

* W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC. W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.

`Microsoft.AspNetCore.Mvc` jest platformą ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles` jest programem obsługi plików statycznych. Aplikacje ASP.NET Core jawnie wybierają oprogramowanie pośredniczące, na przykład w celu obsługi plików statycznych. Aby uzyskać więcej informacji, zobacz [pliki statyczne](xref:fundamentals/static-files).

* Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozszerzenia dodaje procedurę obsługi pliku statycznego. `UseMvc`Metoda rozszerzenia dodaje Routing. Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Dodawanie kontrolera i widoku

W tej sekcji zostanie dodany minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków migrowanych w następnej sekcji.

* Dodaj katalog *kontrolerów* .

* Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do katalogu *controllers* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* Dodaj katalog *widoków* .

* Dodaj *widok/katalog macierzysty* .

* Dodaj **Razor Widok** o nazwie *index. cshtml* do *widoków/katalogu macierzystego* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

Poniżej przedstawiono strukturę projektu:

![Eksplorator rozwiązań pokazywania plików i katalogów WebApp1](mvc/_static/project-structure-controller-view.png)

Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym znacznikiem:

```html
<h1>Hello world!</h1>
```

Uruchom aplikację.

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview).

Poniższe funkcje wymagają migracji z przykładowego projektu MVC ASP.NET do projektu ASP.NET Core:

* zawartość po stronie klienta (CSS, Fonts i scripts)

* kontrolery

* widoki

* modele

* tworzenia pakietów

* filtry

* Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).

## <a name="controllers-and-views"></a>Kontrolery i widoki

* Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej `HomeController` . W ASP.NET MVC typ zwracany metody akcji kontrolera wbudowanego szablonu to <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; w ASP.NET Core MVC, zamiast tego zwracają metody akcji `IActionResult` . `ActionResult` implementuje `IActionResult` , dlatego nie trzeba zmieniać zwracanego typu metod akcji.

* Skopiuj pliki *. cshtml* , *Contact. cshtml* i *Index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.

## <a name="test-each-method"></a>Przetestuj każdą metodę

Plik i style układu nie zostały jeszcze zmigrowane, więc renderowane widoki zawierają tylko zawartość w plikach widoku. Linki do pliku układu dla `About` `Contact` widoków i nie będą jeszcze dostępne.

* Wywołaj renderowane widoki z przeglądarki w działającej aplikacji ASP.NET Core, zastępując bieżący numer portu numerem portu używanym w projekcie ASP.NET Core. Na przykład: `https://localhost:44375/home/about`.

![Strona kontaktu](mvc/_static/contact-page.png)

Zwróć uwagę na brak stylów i elementów menu. Styl zostanie rozwiązany w następnej sekcji.

## <a name="static-content"></a>Zawartość statyczna

W ASP.NET MVC 5 i starszych zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana przy użyciu plików po stronie serwera. W ASP.NET Core zawartość statyczna jest hostowana w katalogu *wwwroot* . Skopiuj zawartość statyczną z aplikacji ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core. W tej przykładowej konwersji:

* Skopiuj plik *favicon. ico* z projektu ASP.NET MVC do katalogu *wwwroot* w projekcie ASP.NET Core.

Projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i przechowuje pliki Bootstrap w katalogach *zawartości* i *skryptów* . Szablon, który wygenerował projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu ( *views/Shared/_Layout. cshtml* ). Pliki *bootstrap.js* i *Bootstrap. css* można SKOPIOWAĆ z projektu ASP.NET MVC do katalogu *wwwroot* w nowym projekcie. Zamiast tego ten dokument dodaje obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN, w następnej sekcji.

## <a name="migrate-the-layout-file"></a>Migrowanie pliku układu

* Skopiuj plik *_ViewStart. cshtml* z katalogu *widoków* projektu ASP.NET MVC do katalogu *widoków* projektu ASP.NET Core. Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.

* Utwórz *widok/katalog udostępniony* .

* *Opcjonalne:* Skopiuj *_ViewImports. cshtml* z katalogu *widoków* projektu *FullAspNetCore* MVC do katalogu *widoków* projektu ASP.NET Core. Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* . Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro). Pomocnicy tagów są używani w nowym pliku układu. Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.

* Skopiuj plik *_Layout. cshtml* z *widoku/udostępnionego* projektu ASP.NET MVC do widoku ASP.NET Core projektu */udostępnionego* katalogu.

Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):

* Zamień na `@Styles.Render("~/Content/css")` `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).

* Usuń `@Scripts.Render("~/bundles/modernizr")` .

* Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@` ). Aby uzyskać więcej informacji, zobacz [Migrowanie uwierzytelniania i Identity ASP.NET Core](xref:migration/identity)

* Zamień `@Scripts.Render("~/bundles/jquery")` na `<script>` element (patrz poniżej).

* Zamień `@Scripts.Render("~/bundles/bootstrap")` na `<script>` element (patrz poniżej).

Znacznik zastępczy dla dołączania do ładowania początkowego:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Znaczniki zamiany dla dołączania jQuery i ładowania początkowego JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Zaktualizowany plik *_Layout. cshtml* jest przedstawiony poniżej:

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Wyświetl witrynę w przeglądarce. Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.

* *Opcjonalne:* Spróbuj użyć nowego pliku układu. Skopiuj plik układu z projektu *FullAspNetCore* . Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie grupowania i minifikacja

Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Rozwiązywanie błędów HTTP 500

Istnieje wiele problemów, które mogą spowodować, że komunikaty o błędach HTTP 500, które nie zawierają żadnych informacji na temat źródła problemu. Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, generowany jest błąd HTTP 500. Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania* konfiguracji. Zobacz przykład w poniższym kodzie:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core konwertuje Nieobsłużone wyjątki na odpowiedzi na błędy HTTP 500. Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze. Aby uzyskać więcej informacji, zobacz [stronę wyjątków dla deweloperów](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
