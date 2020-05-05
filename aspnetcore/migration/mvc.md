---
title: Migrowanie z ASP.NET MVC do ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak rozpocząć Migrowanie projektu ASP.NET MVC do ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777049"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrowanie z ASP.NET MVC do ASP.NET Core MVC

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Kowalski](https://ardalis.com/)i [Scott Addie](https://scottaddie.com)

W tym artykule pokazano, jak rozpocząć Migrowanie projektu ASP.NET MVC do [ASP.NET Core MVC](../mvc/overview.md). W procesie wyróżniamy wiele elementów, które uległy zmianie z ASP.NET MVC. Migrowanie z ASP.NET MVC jest procesem wielu kroków, a w tym artykule omówiono początkową konfigurację, podstawowe kontrolery i widoki, zawartość statyczną i zależności po stronie klienta. Dodatkowe artykuły obejmują Migrowanie konfiguracji i kodu tożsamości znalezionych w wielu projektach ASP.NET MVC.

> [!NOTE]
> Numery wersji w przykładach mogą być nieaktualne. Może być konieczne odpowiednio zaktualizowanie projektów.

## <a name="create-the-starter-aspnet-mvc-project"></a>Tworzenie projektu Starter ASP.NET MVC

Aby zademonstrować uaktualnienie, zaczniemy od utworzenia aplikacji ASP.NET MVC. Utwórz go przy użyciu nazwy *WebApp1* , tak aby przestrzeń nazw była zgodna z ASP.NET Core projektem tworzonym w następnym kroku.

![Okno dialogowe nowego projektu programu Visual Studio](mvc/_static/new-project.png)

![Okno dialogowe Nowa aplikacja sieci Web: szablon projektu MVC wybrany w panelu szablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Opcjonalne:* Zmień nazwę rozwiązania z *WebApp1* na *Mvc5*. Program Visual Studio wyświetla nową nazwę rozwiązania (*Mvc5*), która ułatwia poinformowanie tego projektu z następnego projektu.

## <a name="create-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nową *pustą* aplikację sieci Web ASP.NET Core o takiej samej nazwie jak w poprzednim projekcie (*WebApp1*), tak aby przestrzenie nazw w obu projektach były zgodne. Posiadanie tego samego obszaru nazw ułatwia kopiowanie kodu między tymi dwoma projektami. Musisz utworzyć ten projekt w innym katalogu niż poprzedni projekt, aby użyć tej samej nazwy.

![Okno dialogowe Nowy projekt](mvc/_static/new_core.png)

![Nowe okno dialogowe aplikacji sieci Web ASP.NET: pusty szablon projektu wybrany w panelu szablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Opcjonalne:* Utwórz nową aplikację ASP.NET Core przy użyciu szablonu projektu *aplikacji sieci Web* . Nazwij projekt *WebApp1*i wybierz opcję uwierzytelniania **poszczególnych kont użytkowników**. Zmień nazwę tej aplikacji na *FullAspNetCore*. Utworzenie tego projektu umożliwia zaoszczędzenie czasu w konwersji. Można przyjrzeć się kodowi generowanemu przez szablon, aby zobaczyć wynik końcowy lub skopiować kod do projektu konwersji. Jest on również przydatny w przypadku zablokowania kroku konwersji w celu porównania z projektem wygenerowanym przez szablon.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurowanie lokacji do korzystania z MVC

::: moniker range=">= aspnetcore-2.1"

* W przypadku określania platformy .NET Core jest przywoływany domyślny [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC. W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* W przypadku określania platformy .NET Core do programu jest przywoływany [pakiet Microsoft. AspNetCore. All](xref:fundamentals/metapackage) . Ten pakiet zawiera pakiety powszechnie używane przez aplikacje MVC. W przypadku .NET Framework określania wartości docelowej odwołania do pakietów muszą być wyszczególnione pojedynczo w pliku projektu.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* W przypadku określania wartości docelowej .NET Core lub .NET Framework pakiety często używane przez aplikacje MVC są wymieniane pojedynczo w pliku projektu.

::: moniker-end

`Microsoft.AspNetCore.Mvc`jest platformą ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles`jest programem obsługi plików statycznych. Środowisko uruchomieniowe ASP.NET Core jest modularne i należy jawnie zadecydować, aby obsługiwały pliki statyczne (zobacz [pliki statyczne](xref:fundamentals/static-files)).

* Otwórz plik *Startup.cs* i Zmień kod w taki sposób, aby był zgodny z następującymi:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

Metoda `UseStaticFiles` rozszerzenia dodaje procedurę obsługi pliku statycznego. Jak wspomniano wcześniej, środowisko uruchomieniowe ASP.NET jest modularne, a użytkownik musi jawnie zadecydować, aby obsługiwał pliki statyczne. Metoda `UseMvc` rozszerzenia dodaje Routing. Aby uzyskać więcej informacji, zobacz Uruchamianie i [Routing](xref:fundamentals/routing) [aplikacji](xref:fundamentals/startup) .

## <a name="add-a-controller-and-view"></a>Dodawanie kontrolera i widoku

W tej sekcji dodasz minimalny kontroler i widok służący jako symbole zastępcze dla kontrolera ASP.NET MVC i widoków, które zostaną zmigrowane w następnej sekcji.

* Dodaj folder *controllers* .

* Dodaj **klasę kontrolera** o nazwie *HomeController.cs* do folderu *controllers* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/add_mvc_ctl.png)

* Dodaj folder *widoki* .

* Dodaj *Widok/folder macierzysty* .

* Dodaj ** Razor widok** o nazwie *index. cshtml* do folderu *widoki/Home* .

![Okno dialogowe Dodawanie nowego elementu](mvc/_static/view.png)

Poniżej przedstawiono strukturę projektu:

![Eksplorator rozwiązań pokazywania plików i folderów WebApp1](mvc/_static/project-structure-controller-view.png)

Zastąp zawartość pliku *viewss/Home/index. cshtml* następującym:

```html
<h1>Hello world!</h1>
```

Uruchom aplikację.

![Aplikacja internetowa otwarta w przeglądarce Microsoft Edge](mvc/_static/hello-world.png)

Aby uzyskać więcej informacji, zobacz [Kontrolery](xref:mvc/controllers/actions) i [widoki](xref:mvc/views/overview) .

Teraz, gdy mamy minimalny działający projekt ASP.NET Core, możemy rozpocząć Migrowanie funkcji z projektu MVC ASP.NET. Musimy przenieść następujące elementy:

* zawartość po stronie klienta (CSS, Fonts i scripts)

* kontrolery

* widoki

* modele

* tworzenia pakietów

* filtry

* Logowanie/wylogowywanie, Identity (to jest wykonywane w następnym samouczku).

## <a name="controllers-and-views"></a>Kontrolery i widoki

* Skopiuj każdą z metod z ASP.NET MVC `HomeController` do nowej. `HomeController` Należy pamiętać, że w ASP.NET MVC Metoda akcji kontrolera wbudowanego szablonu ma wartość [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); w ASP.NET Core MVC, zamiast tego zwracają `IActionResult` metody akcji. `ActionResult`implementuje `IActionResult`, dlatego nie trzeba zmieniać zwracanego typu metod akcji.

* Skopiuj pliki *. cshtml*, *Contact. cshtml*i *index. cshtml* Razor z projektu ASP.NET MVC do projektu ASP.NET Core.

* Uruchom aplikację ASP.NET Core i przetestuj każdą metodę. Plik lub style układu nie został jeszcze zmigrowany, więc renderowane widoki zawierają tylko zawartość w plikach widoku. Nie masz linków do pliku układu dla widoków `About` i `Contact` , więc musisz wywołać je z przeglądarki (Zastąp **4492** numerem portu używanym w projekcie).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Strona kontaktu](mvc/_static/contact-page.png)

Zwróć uwagę na brak stylów i elementów menu. Naprawimy to w następnej sekcji.

## <a name="static-content"></a>Zawartość statyczna

W poprzednich wersjach ASP.NET MVC zawartość statyczna była hostowana z poziomu korzenia projektu sieci Web i była mieszana z plikami po stronie serwera. W ASP.NET Core zawartość statyczna jest hostowana w folderze *wwwroot* . Chcesz skopiować zawartość statyczną ze starej aplikacji ASP.NET MVC do folderu *wwwroot* w projekcie ASP.NET Core. W tej przykładowej konwersji:

* Skopiuj plik *favicon. ico* ze starego projektu MVC do folderu *wwwroot* w projekcie ASP.NET Core.

Stary projekt ASP.NET MVC używa narzędzia [Bootstrap](https://getbootstrap.com/) do jego stylu i zapisuje pliki Bootstrap w folderach *zawartości* i *skryptów* . Szablon, który wygenerował stary projekt ASP.NET MVC, odwołuje się do Bootstrap w pliku układu (*views/Shared/_Layout. cshtml*). Można skopiować pliki *Bootstrap. js* i *Bootstrap. css* z projektu ASP.NET MVC do folderu *wwwroot* w nowym projekcie. Zamiast tego dodamy obsługę ładowania początkowego (i innych bibliotek po stronie klienta) przy użyciu sieci CDN w następnej sekcji.

## <a name="migrate-the-layout-file"></a>Migrowanie pliku układu

* Skopiuj plik *_ViewStart. cshtml* ze starego folderu *widoków* projektu ASP.NET MVC do folderu *widoków* projektu ASP.NET Core. Plik *_ViewStart. cshtml* nie został zmieniony w ASP.NET Core MVC.

* Utwórz *Widok/folder udostępniony* .

* *Opcjonalne:* Skopiuj *_ViewImports. cshtml* z folderu *widoków* projektu *FullAspNetCore* MVC do folderu *widoków* projektu ASP.NET Core. Usuń deklarację przestrzeni nazw w pliku *_ViewImports. cshtml* . Plik *_ViewImports. cshtml* zawiera przestrzenie nazw dla wszystkich plików widoków i wywołuje [pomocników tagów](xref:mvc/views/tag-helpers/intro). Pomocnicy tagów są używani w nowym pliku układu. Plik *_ViewImports. cshtml* jest nowy dla ASP.NET Core.

* Skopiuj plik *_Layout. cshtml* ze starego *widoku/folderu udostępnionego* projektu MVC ASP.NET do folderu *widoki/udostępniony* projektu ASP.NET Core.

Otwórz plik *_Layout. cshtml* i wprowadź następujące zmiany (kod wykonany jest przedstawiony poniżej):

* Zamień `@Styles.Render("~/Content/css")` na `<link>` element, który ma ładować *Bootstrap. css* (patrz poniżej).

* Usuń `@Scripts.Render("~/bundles/modernizr")`.

* Dodaj komentarz do `@Html.Partial("_LoginPartial")` wiersza (Otocz linią `@*...*@`). Aby uzyskać więcej informacji [, zobacz Migrowanie uwierzytelniania Identity i ASP.NET Core](xref:migration/identity)

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

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Wyświetl witrynę w przeglądarce. Powinien być teraz poprawnie załadowany przy użyciu oczekiwanych stylów.

* *Opcjonalne:* Warto spróbować użyć nowego pliku układu. Dla tego projektu można skopiować plik układu z projektu *FullAspNetCore* . Nowy plik układu używa [pomocników tagów](xref:mvc/views/tag-helpers/intro) i ma inne ulepszenia.

## <a name="configure-bundling-and-minification"></a>Konfigurowanie grupowania i minifikacja

Aby uzyskać informacje o sposobie konfigurowania grupowania i minifikacja, zobacz Tworzenie [i minifikacja](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Rozwiązywanie błędów HTTP 500

Istnieje wiele problemów, które mogą spowodować wyświetlenie komunikatu o błędzie HTTP 500, który nie zawiera żadnych informacji na temat źródła problemu. Na przykład, jeśli plik *views/_ViewImports. cshtml* zawiera przestrzeń nazw, która nie istnieje w projekcie, zostanie wyświetlony błąd HTTP 500. Domyślnie w aplikacjach ASP.NET Core `UseDeveloperExceptionPage` rozszerzenie jest dodawane do `IApplicationBuilder` i wykonywane podczas *opracowywania*konfiguracji. Jest to szczegółowo opisany w poniższym kodzie:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core konwertuje Nieobsłużone wyjątki w aplikacji sieci Web do odpowiedzi na błędy HTTP 500. Zwykle szczegóły błędu nie są uwzględniane w tych odpowiedziach, aby zapobiec ujawnieniu potencjalnie poufnych informacji o serwerze. Aby uzyskać więcej informacji **, zobacz Używanie strony wyjątków dla deweloperów** w temacie [Obsługa błędów](../fundamentals/error-handling.md) .

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
