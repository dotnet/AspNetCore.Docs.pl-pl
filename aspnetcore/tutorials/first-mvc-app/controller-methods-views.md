---
title: Część 6, metody kontrolera i widoki w ASP.NET Core
author: rick-anderson
description: Część 6 Dodaj model do aplikacji ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050774"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>Część 6, metody kontrolera i widoki w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Dobrze zaczynasz korzystać z aplikacji filmowej, ale prezentacja nie jest idealna, na przykład **ReleaseDate** powinny być dwa słowa.

![Widok indeksu: Data wydania to jeden wyraz (bez spacji), a każda Data wydania filmu przedstawia godzinę 12 AM](working-with-sql/_static/m55.png)

Otwórz plik *models/Movie. cs* i Dodaj wyróżnione wiersze poniżej:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

W następnym samouczku omówiono wszystkie [Adnotacje](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) . Atrybut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). Atrybut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).

Przejdź do `Movies` kontrolera i przytrzymaj wskaźnik myszy nad linkiem **edycji** , aby zobaczyć docelowy adres URL.

![Okno przeglądarki z myszą nad linkiem edycji i pokazanym adresem URL linku https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Linki **Edytuj**, **szczegóły** i **Usuń** są generowane przez pomocnika podstawowego tagu zakotwiczenia MVC w pliku *views/filmy/index. cshtml* .

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) włączają kod po stronie serwera, aby uczestniczyć w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach. W powyższym kodzie, `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML z metody akcji kontrolera i identyfikatora trasy. Możesz użyć **widoku źródła** z ulubionej przeglądarki lub użyć narzędzi programistycznych do sprawdzenia wygenerowanego znacznika. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Odwołaj format zestawu [routingu](xref:mvc/controllers/routing) w pliku *Startup.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core przetłumaczy `https://localhost:5001/Movies/Edit/4` żądanie na `Edit` metodę akcji `Movies` kontrolera z parametrem `Id` 4. (Metody kontrolera są również nazywane metodami akcji).

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) to jedna z najpopularniejszych nowych funkcji w programie ASP.NET Core. Aby uzyskać więcej informacji, zobacz [dodatkowe zasoby](#additional-resources).

<a name="get-post"></a>

Otwórz `Movies` kontroler i Przeanalizuj dwie `Edit` metody akcji. Poniższy kod przedstawia `HTTP GET Edit` metodę, która pobiera film i wypełnia formularz edycji wygenerowany przez plik *Edit. cshtml* Razor .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Poniższy kod przedstawia `HTTP POST Edit` metodę, która przetwarza ogłoszone wartości filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Poniższy kod przedstawia `HTTP POST Edit` metodę, która przetwarza ogłoszone wartości filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Ten `[Bind]` atrybut jest jednym ze sposobów ochrony przed [nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Należy uwzględnić tylko właściwości w `[Bind]` atrybucie, który ma zostać zmieniony. Aby uzyskać więcej informacji, zobacz [Ochrona kontrolera przed nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [Modele widoków](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) zapewniają alternatywne podejście do zapobiegania nadmiernemu księgowaniu.

Zwróć uwagę, że druga `Edit` Metoda działania jest poprzedzona `[HttpPost]` atrybutem.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost`Ten atrybut określa, że ta `Edit` Metoda może być wywoływana *tylko* w przypadku `POST` żądań. Można zastosować `[HttpGet]` atrybut do pierwszej metody edycji, ale nie jest to konieczne, ponieważ jest to `[HttpGet]` wartość domyślna.

Ten `ValidateAntiForgeryToken` atrybut służy do [zapobiegania fałszerstwu żądania](xref:security/anti-request-forgery) i jest sparowany z tokenem chroniącym przed fałszerstwem wygenerowanym w pliku widoku edycji (*widoki/filmy/Edit. cshtml*). Plik widoku edycji generuje token chroniący przed fałszerstwem za pomocą [pomocnika tagu formularza](xref:mvc/views/working-with-forms).

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Pomocnik tagu formularza](xref:mvc/views/working-with-forms) generuje ukryty token chroniący przed fałszerstwem, który musi być zgodny z `[ValidateAntiForgeryToken]` wygenerowanym tokenem chroniącym przed fałszerstwem w `Edit` metodzie kontrolera filmów. Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.

`HttpGet Edit`Metoda przyjmuje `ID` parametr filmu, wyszukuje film przy użyciu metody Entity Framework `FindAsync` i zwraca wybrany film do widoku edycji. Jeśli nie można znaleźć filmu, `NotFound` zwracany jest (HTTP 404).

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Gdy system szkieletu utworzył widok edycji, zbadał `Movie` klasę i utworzony kod w celu renderowania `<label>` i `<input>` elementów dla każdej właściwości klasy. W poniższym przykładzie przedstawiono widok edycji, który został wygenerowany przez system szkieletu programu Visual Studio:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Zwróć uwagę, jak szablon widoku zawiera `@model MvcMovie.Models.Movie` instrukcję w górnej części pliku. `@model MvcMovie.Models.Movie` Określa, że widok oczekuje modelu dla szablonu widoku, który ma być typu `Movie` .

Kod szkieletowy używa kilku metod pomocnika tagów do uproszczenia znacznika HTML. [Pomocnik tagu etykiety](xref:mvc/views/working-with-forms) wyświetla nazwę pola ("title", "ReleaseDate", "gatunek" lub "price"). [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) renderuje `<input>` element HTML. [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms) wyświetla wszystkie komunikaty weryfikacyjne skojarzone z tą właściwością.

Uruchom aplikację i przejdź do `/Movies` adresu URL. Kliknij link **Edytuj** . Sprawdź Źródło strony w przeglądarce. Wygenerowany kod HTML dla `<form>` elementu jest przedstawiony poniżej.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>`Elementy znajdują się w `HTML <form>` elemencie, którego `action` atrybut ma ustawioną wartość post na `/Movies/Edit/id` adres URL. Dane formularza zostaną opublikowane na serwerze po `Save` kliknięciu przycisku. Ostatni wiersz przed zamykającym `</form>` elementu pokazuje ukryty token [XSRF](xref:security/anti-request-forgery) wygenerowany przez [pomocnika tagów formularza](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Przetwarzanie żądania POST

Na poniższej liście przedstawiono `[HttpPost]` wersję `Edit` metody akcji.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Ten `[ValidateAntiForgeryToken]` atrybut sprawdza poprawność ukrytego tokenu [XSRF](xref:security/anti-request-forgery) wygenerowanego przez generatora tokenów chroniących przed fałszowaniem w [Pomocniku tagów formularza](xref:mvc/views/working-with-forms)

System [powiązań modelu](xref:mvc/models/model-binding) przyjmuje wartości ogłoszonych formularzy i tworzy `Movie` obiekt, który jest przesyłany jako `movie` parametr. `ModelState.IsValid`Właściwość weryfikuje, że dane przesłane w formularzu mogą służyć do modyfikowania (edycji lub aktualizowania) `Movie` obiektu. Jeśli dane są prawidłowe, zostaną zapisane. Zaktualizowane (edytowane) dane filmu są zapisywane w bazie danych przez wywołanie `SaveChangesAsync` metody kontekstu bazy danych. Po zapisaniu danych kod przekierowuje użytkownika do `Index` metody akcji `MoviesController` klasy, która wyświetla kolekcję filmów, włącznie z wprowadzonymi zmianami.

Przed opublikowaniem formularza na serwerze sprawdzanie poprawności po stronie klienta sprawdza wszystkie reguły sprawdzania poprawności w polach. Jeśli wystąpią jakieś błędy sprawdzania poprawności, zostanie wyświetlony komunikat o błędzie z informacją, że formularz nie zostanie opublikowany. Jeśli język JavaScript jest wyłączony, nie będzie można sprawdzić poprawności po stronie klienta, ale serwer wykryje ogłoszone wartości, które nie są prawidłowe, a wartości formularza zostaną wyświetlone ponownie przy użyciu komunikatów o błędach. W dalszej części samouczka sprawdzimy [Sprawdzanie poprawności modelu](xref:mvc/models/validation) w bardziej szczegółowy sposób. [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms) w szablonie *widoki/filmy/edytowanie. cshtml* ma zadbać o wyświetlenie odpowiednich komunikatów o błędach.

![Widok edycji: wyjątek dla nieprawidłowej wartości ceny ABC wskazuje, że cena pola musi być liczbą. Wyjątek dla nieprawidłowej wartości daty wydania dla Stanów XYZ Wprowadź prawidłową datę.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Wszystkie `HttpGet` metody w kontrolerze filmu są zgodne z podobnym wzorcem. Uzyskują one obiekt filmu (lub listę obiektów w przypadku `Index` ) i przekazują obiekt (model) do widoku. `Create`Metoda przekazuje pusty obiekt filmu do `Create` widoku. Wszystkie metody, które tworzą, edytują, usuwają lub w inny sposób modyfikują dane, to w ramach `[HttpPost]` przeciążenia metody. Modyfikowanie danych w `HTTP GET` metodzie stanowi zagrożenie bezpieczeństwa. Modyfikowanie danych w `HTTP GET` metodzie również narusza najlepsze rozwiązania protokołu HTTP i wzorzec [rest](http://rest.elkstein.org/) architektury, który określa, że żądania GET nie powinny zmieniać stanu aplikacji. Innymi słowy wykonanie operacji GET powinno być operacją bezpieczną, która nie ma efektów ubocznych i nie modyfikuje utrwalonych danych.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Globalizacja i lokalizacja](xref:fundamentals/localization)
* [Wprowadzenie do pomocy tagów](xref:mvc/views/tag-helpers/intro)
* [Autorzy tagów](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* Chroń kontroler przed [nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [Modele widoków](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Pomocnik tagu formularza](xref:mvc/views/working-with-forms)
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms)
* [Pomocnik tagu etykiety](xref:mvc/views/working-with-forms)
* [Pomocnik tagu wyboru](xref:mvc/views/working-with-forms)
* [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Poprzedni](working-with-sql.md) 
>  [Dalej](search.md)  
