---
title: Metody i widoki kontrolera w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak pracować z metodami kontrolera, widokami ianotacjami danych w ASP.NET Core.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 87b3cb2f4429157123d30274d1f12cd589c1cc99
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242513"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>Metody i widoki kontrolera w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Mamy dobry początek aplikacji filmowej, ale prezentacja nie jest idealna, na przykład **ReleaseDate** powinno być dwoma słowami.

![Widok indeksu: Data wydania to jedno słowo (bez spacji), a każda data premiery filmu pokazuje godzinę 12:00](working-with-sql/_static/m55.png)

Otwórz plik *Models/Movie.cs* i dodaj podświetlone linie pokazane poniżej:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Omówimy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) w następnym samouczku. [Atrybut Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) określa, co ma być wyświetlane dla nazwy pola (w tym przypadku "Data wydania" zamiast "ReleaseDate"). [Atrybut DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) określa typ danych (Data), więc informacje o czasie przechowywane w polu nie są wyświetlane.

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych jest wymagana, aby entity `Price` Framework Core można poprawnie mapować do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).

Przejdź do `Movies` kontrolera i przytrzymaj wskaźnik myszy nad łączem **Edytuj,** aby wyświetlić docelowy adres URL.

![Okno przeglądarki z kursorem myszy na https://localhost:5001/Movies/Edit/5 łącze Edytuj i wyświetlonym adresem URL łącza](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Łącza **Edytuj,** **Szczegóły**i **Usuń** są generowane przez pomocnika znacznika kotwicy Core MVC w pliku *Views/Movies/Index.cshtml.*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor. W powyższym kodzie `AnchorTagHelper` dynamicznie generuje `href` wartość atrybutu HTML z metody akcji kontrolera i identyfikator trasy. Użyj **view source** z ulubionej przeglądarki lub narzędzia deweloperskie do zbadania wygenerowanych znaczników. Poniżej przedstawiono część wygenerowanego kodu HTML:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Przypomnij sobie format [routingu](xref:mvc/controllers/routing) ustawiony w pliku *Startup.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core przekłada się `https://localhost:5001/Movies/Edit/4` na żądanie `Edit` do metody `Movies` akcji kontrolera `Id` z parametrem 4. (Metody kontrolera są również nazywane metodami akcji).

[Tag Helpers](xref:mvc/views/tag-helpers/intro) to jedna z najpopularniejszych nowych funkcji w ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Dodatkowe zasoby](#additional-resources).

<a name="get-post"></a>

Otwórz `Movies` kontroler i sprawdź `Edit` dwie metody akcji. Poniższy kod `HTTP GET Edit` przedstawia metodę, która pobiera film i wypełnia formularz edycji wygenerowany przez plik *Edit.cshtml* Razor.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Poniższy kod `HTTP POST Edit` przedstawia metodę, która przetwarza zaksięgowane wartości filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Poniższy kod `HTTP POST Edit` przedstawia metodę, która przetwarza zaksięgowane wartości filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Atrybut `[Bind]` jest jednym ze sposobów ochrony przed [nadmiernym księgowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Należy uwzględnić tylko właściwości `[Bind]` w atrybucie, który chcesz zmienić. Aby uzyskać więcej informacji, zobacz [Ochrona kontrolera przed nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) zapewniają alternatywne podejście, aby zapobiec nadmiernemu księgowaniu.

Zwróć uwagę, że druga `Edit` metoda `[HttpPost]` akcji jest poprzedzona atrybutem.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

Atrybut `HttpPost` określa, że `Edit` ta metoda może być `POST` wywoływana *tylko* dla żądań. Można zastosować `[HttpGet]` atrybut do pierwszej metody edycji, ale nie `[HttpGet]` jest to konieczne, ponieważ jest to ustawienie domyślne.

Atrybut `ValidateAntiForgeryToken` jest używany do [zapobiegania fałszerskości żądania](xref:security/anti-request-forgery) i jest sparowany z tokenem anty-fałszerstwa wygenerowanym w pliku widoku edycji *(Views/Movies/Edit.cshtml*). Plik widoku edycji generuje token antyzwiązany z fałszerską za pomocą [pomocnika znacznika formularza](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Pomocnik tagu formularza](xref:mvc/views/working-with-forms) generuje ukryty token anty-fałszerstwa, który musi odpowiadać wygenerowanemu `[ValidateAntiForgeryToken]` tokenowi `Edit` anty-fałszerstwa w metodzie kontrolera Filmy. Aby uzyskać więcej informacji, zobacz [Forgery przeciw żądaniem](xref:security/anti-request-forgery).

Metoda `HttpGet Edit` przyjmuje parametr `ID` filmu, wyszukuje film `FindAsync` przy użyciu metody Entity Framework i zwraca wybrany film do widoku Edycji. Jeśli nie można odnaleźć filmu, `NotFound` zostanie zwrócony (HTTP 404).

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Gdy system rusztowania utworzył widok Edycja, `Movie` zbadał klasę i `<label>` `<input>` utworzył kod do renderowania i elementów dla każdej właściwości klasy. W poniższym przykładzie przedstawiono widok edycji, który został wygenerowany przez system szkieletowania programu Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Zwróć uwagę, jak `@model MvcMovie.Models.Movie` szablon widoku ma instrukcję w górnej części pliku. `@model MvcMovie.Models.Movie`określa, że widok oczekuje, że model szablonu `Movie`widoku będzie typu .

Szkieletowy kod używa kilku metod Pomocnika znaczników, aby usprawnić znaczniki HTML. - [Pomocnik znacznika etykiety](xref:mvc/views/working-with-forms) wyświetla nazwę pola ("Title", "ReleaseDate", "Genre" lub "Price"). [Pomocnik znacznika wejściowego](xref:mvc/views/working-with-forms) renderuje element HTML. `<input>` [Pomocnik tagu sprawdzania poprawności](xref:mvc/views/working-with-forms) wyświetla wszystkie komunikaty sprawdzania poprawności skojarzone z tą właściwością.

Uruchom aplikację i przejdź `/Movies` do adresu URL. Kliknij łącze **Edytuj.** W przeglądarce wyświetl źródło strony. Wygenerowany kod `<form>` HTML dla elementu jest pokazany poniżej.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Elementy `<input>` znajdują się `HTML <form>` w `action` elemencie, którego `/Movies/Edit/id` atrybut jest ustawiony na wpis do adresu URL. Dane formularza zostaną opublikowane na serwerze po kliknięciu przycisku. `Save` Ostatni wiersz przed `</form>` elementem zamknięcia pokazuje ukryty token [XSRF](xref:security/anti-request-forgery) wygenerowany przez [Pomocnika znacznika formularza](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Przetwarzanie żądania POST

Na poniższej `[HttpPost]` liście przedstawiono wersję metody `Edit` akcji.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Atrybut `[ValidateAntiForgeryToken]` sprawdza poprawność ukrytego tokenu [XSRF](xref:security/anti-request-forgery) wygenerowanego przez generator tokenów antyfałszerstwa w [Pomocniku tagu formularza](xref:mvc/views/working-with-forms)

System [wiązania modelu](xref:mvc/models/model-binding) przyjmuje wartości zaksięgowanych formularzy i tworzy `Movie` obiekt, który jest przekazywany jako `movie` parametr. Metoda `ModelState.IsValid` sprawdza, czy dane przesłane w formularzu mogą służyć do `Movie` modyfikowania (edytowania lub aktualizowania) obiektu. Jeśli dane są prawidłowe, są zapisywane. Zaktualizowane (edytowane) dane filmowe są zapisywane w bazie danych przez wywołanie `SaveChangesAsync` metody kontekstu bazy danych. Po zapisaniu danych kod przekierowuje `Index` użytkownika do `MoviesController` metody akcji klasy, która wyświetla kolekcję filmów, w tym wprowadzone właśnie zmiany.

Przed zaksięgowanie formularza na serwerze sprawdzanie poprawności po stronie klienta sprawdza wszystkie reguły sprawdzania poprawności pól. Jeśli występują błędy sprawdzania poprawności, wyświetlany jest komunikat o błędzie, a formularz nie jest księgowany. Jeśli javascript jest wyłączony, nie będzie mieć sprawdzania poprawności po stronie klienta, ale serwer wykryje opublikowane wartości, które nie są prawidłowe, a wartości formularza zostaną ponownie wyświetlone za pomocą komunikatów o błędach. W dalszej części samouczka przyjrzymy [się weryfikacji modelu](xref:mvc/models/validation) bardziej szczegółowo. [Pomocnik znaczników sprawdzania poprawności](xref:mvc/views/working-with-forms) w szablonie widoku *Widoki/Filmy/Edit.cshtml* zajmuje się wyświetlaniem odpowiednich komunikatów o błędach.

![Edytuj widok: Wyjątek dla niepoprawnej wartości ceny abc stwierdza, że cena pola musi być liczbą. Wyjątek dla niepoprawnej wartości daty wydania xyz states Wprowadź prawidłową datę.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Wszystkie `HttpGet` metody w kontrolerze filmu są zgodne z podobnym wzorcem. Otrzymują obiekt filmu (lub listę obiektów, w `Index`przypadku ) i przekazują obiekt (model) do widoku. Metoda `Create` przekazuje pusty obiekt filmu `Create` do widoku. Wszystkie metody, które tworzą, edytują, delegowają `[HttpPost]` lub w inny sposób modyfikują dane, robią to w przeciążeniu metody. Modyfikowanie danych `HTTP GET` w metodzie stanowi zagrożenie bezpieczeństwa. Modyfikowanie danych `HTTP GET` w metodzie narusza również najlepsze rozwiązania HTTP i wzorzec [odbytniczego](http://rest.elkstein.org/) architektury, który określa, że żądania GET nie powinny zmieniać stanu aplikacji. Innymi słowy wykonywanie operacji GET powinna być bezpieczną operacją, która nie ma żadnych skutków ubocznych i nie modyfikuje utrwalonych danych.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Globalizacja i lokalizacja](xref:fundamentals/localization)
* [Wprowadzenie do pomocników tagów](xref:mvc/views/tag-helpers/intro)
* [Pomocnicy tagów autora](xref:mvc/views/tag-helpers/authoring)
* [Fałszerstwo przeciwuszcjowe](xref:security/anti-request-forgery)
* Ochrona kontrolera przed [nadmiernym publikowaniem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModelki](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Pomocnik tagu formularza](xref:mvc/views/working-with-forms)
* [Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms)
* [Pomocnik tagu etykiety](xref:mvc/views/working-with-forms)
* [Pomocnik tagu wyboru](xref:mvc/views/working-with-forms)
* [Pomocnik tagu sprawdzania poprawności](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Poprzedni](working-with-sql.md)
> [następny](search.md)  
