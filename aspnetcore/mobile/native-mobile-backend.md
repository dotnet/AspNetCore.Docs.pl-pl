---
title: Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć usługi zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564019"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core

Za pomocą [Kuba Montemagno](https://twitter.com/JamesMontemagno)

Aplikacje mobilne mogą komunikować się z ASP.NET Core usług zaplecza. Aby uzyskać instrukcje dotyczące łączenia lokalnych usług sieci Web z symulatorami systemu iOS i emulatorami systemu Android, zobacz [nawiązywanie połączenia z lokalnymi usługami sieci Web z symulatorów systemu iOS i emulatorów systemu Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Wyświetlanie lub Pobieranie przykładowego kodu usług wewnętrznej bazy danych](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>Przykładowa Natywna aplikacja mobilna

W tym samouczku przedstawiono sposób tworzenia usług zaplecza przy użyciu ASP.NET Core do obsługi natywnych aplikacji mobilnych. Korzysta ona z [aplikacji Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako klienta natywnego, w tym oddzielnych klientów natywnych dla systemów Android, iOS i Windows. Aby utworzyć aplikację natywną (i zainstalować niezbędne bezpłatne narzędzia Xamarin), możesz skorzystać z połączonego samouczka, a także pobrać przykładowe rozwiązanie Xamarin. Przykład platformy Xamarin zawiera projekt usług internetowego interfejsu API ASP.NET Core, którego aplikacja ASP.NET Core w tym artykule zastępuje (bez zmian wymaganych przez klienta).

![Aby aplikacja REST działała na urządzeniu smartphone z systemem Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Funkcje

[Aplikacja TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) obsługuje wyświetlanie list, Dodawanie, usuwanie i aktualizowanie elementów To-Do. Każdy element ma identyfikator, nazwę, notatki i Właściwość wskazującą, czy została jeszcze ukończona.

Główny widok elementów, jak pokazano powyżej, zawiera listę nazw poszczególnych elementów i wskazuje, czy jest on wykonany za pomocą znacznika wyboru.

Naciśnięcie `+` ikony powoduje otwarcie okna dialogowego Dodawanie elementu:

![Okno dialogowe Dodawanie elementu](native-mobile-backend/_static/todo-android-new-item.png)

Naciśnięcie elementu na głównym ekranie listy otwiera okno dialogowe edytowania, w którym można modyfikować nazwę elementu, notatki i gotowe ustawienia lub można usunąć element:

![Edytowanie elementu — okno dialogowe](native-mobile-backend/_static/todo-android-edit-item.png)

Aby przetestować aplikację ASP.NET Core utworzoną w następnej sekcji działającej na komputerze, zaktualizuj [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) stałą aplikacji.

Emulatory systemu Android nie są uruchamiane na maszynie lokalnej i używają adresu IP sprzężenia zwrotnego (10.0.2.2) do komunikacji z maszyną lokalną. Aby wykryć, w jaki sposób działa system, aby użyć poprawnego adresu URL, Skorzystaj z platformy [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) .

Przejdź do [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projektu i Otwórz [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) plik. Plik *Constants.cs* zawiera następującą konfigurację.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

Opcjonalnie można wdrożyć usługę sieci Web w usłudze w chmurze, takiej jak Azure, i zaktualizować `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nową aplikację sieci Web ASP.NET Core w programie Visual Studio. Wybierz szablon internetowego interfejsu API. Nazwij projekt *TodoAPI*.

![Okno dialogowe Nowa aplikacja sieci Web ASP.NET z wybranym szablonem projektu interfejsu API sieci Web](native-mobile-backend/_static/web-api-template.png)

Aplikacja powinna odpowiedzieć na wszystkie żądania kierowane do portu 5000, w tym zwykły tekst http dla naszego klienta mobilnego. Aktualizacja *Startup.cs* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> nie zostanie uruchomiona w programie Development:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Uruchom aplikację bezpośrednio, a nie za IIS Express. IIS Express domyślnie ignoruje żądania nielokalne. Uruchom polecenie [dotnet Run](/dotnet/core/tools/dotnet-run) z wiersza polecenia lub wybierz profil nazwy aplikacji z listy rozwijanej cel debugowania na pasku narzędzi programu Visual Studio.

Dodaj klasę modelu, aby reprezentować elementy To-Do. Oznacz wymagane pola `[Required]` atrybutem:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Metody interfejsu API wymagają pewnego sposobu pracy z danymi. Użyj tego samego `ITodoRepository` interfejsu, z którego korzysta oryginalny przykład platformy Xamarin:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

Dla tego przykładu implementacja używa tylko prywatnej kolekcji elementów:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Skonfiguruj implementację w programie *Startup.cs*:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Tworzenie kontrolera

Dodaj nowy kontroler do projektu, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs). Powinien dziedziczyć po <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Dodaj `Route` atrybut, aby wskazać, że kontroler będzie obsługiwać żądania wysyłane do ścieżek zaczynających się od `api/todoitems` . `[controller]`Token w marszrucie jest zastępowany nazwą kontrolera (z pominięciem `Controller` sufiksu) i jest szczególnie przydatny w przypadku tras globalnych. Dowiedz się więcej o [routingu](../fundamentals/routing.md).

Kontroler wymaga `ITodoRepository` funkcji do; Zażądaj wystąpienia tego typu za pomocą konstruktora kontrolera. W czasie wykonywania to wystąpienie zostanie dostarczone przy użyciu obsługi platformy w celu [iniekcji zależności](../fundamentals/dependency-injection.md).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Ten interfejs API obsługuje cztery różne czasowniki HTTP do wykonywania operacji CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie) w źródle danych. Najprostszą z nich jest operacja odczytu, która odnosi się do żądania HTTP GET.

### <a name="reading-items"></a>Odczytywanie elementów

Żądanie listy elementów jest wykonywane z żądaniem GET do `List` metody. `[HttpGet]`Atrybut `List` metody wskazuje, że ta akcja powinna obsługiwać tylko żądania GET. Trasa dla tej akcji jest trasą określoną na kontrolerze. Nie trzeba koniecznie używać nazwy akcji jako części trasy. Wystarczy upewnić się, że każda akcja ma unikatową i jednoznaczną trasę. Atrybuty routingu mogą być stosowane zarówno na poziomie kontrolera, jak i metody do tworzenia określonych tras.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

`List`Metoda zwraca kod odpowiedzi 200 OK i wszystkie elementy do wykonania, zserializowane jako JSON.

Nową metodę interfejsu API można testować przy użyciu różnych narzędzi, takich jak program [Poster](https://www.getpostman.com/docs/), jak pokazano tutaj:

![Konsola programu Poster pokazująca żądanie GET dla TodoItems i treść odpowiedzi przedstawiającą kod JSON dla trzech zwróconych elementów](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Tworzenie elementów

Zgodnie z Konwencją tworzenie nowych elementów danych jest mapowane na zlecenie HTTP POST. `Create` `[HttpPost]` Do metody zastosowano atrybut i zaakceptowano `TodoItem` wystąpienie. Ponieważ `item` argument jest przesyłany w treści wpisu, ten parametr określa `[FromBody]` atrybut.

Wewnątrz metody element jest sprawdzany pod kątem ważności i wcześniejszej istnienia w magazynie danych, a jeśli nie wystąpią żadne problemy, zostanie dodany za pomocą repozytorium. Sprawdzanie `ModelState.IsValid` sprawdza [poprawność modelu](../mvc/models/validation.md)i należy je wykonać w każdej metodzie interfejsu API, która akceptuje dane wejściowe użytkownika.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

W przykładzie używane `enum` są zawierające kody błędów, które są przesyłane do klienta mobilnego:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Przetestuj Dodawanie nowych elementów przy użyciu programu Poster, wybierając pozycję POST Verb dostarczającą nowy obiekt w formacie JSON w treści żądania. Należy również dodać nagłówek żądania określający `Content-Type` `application/json` .

![Konsola programu Poster pokazująca wpis i odpowiedź](native-mobile-backend/_static/postman-post.png)

Metoda zwraca nowo utworzony element w odpowiedzi.

### <a name="updating-items"></a>Aktualizowanie elementów

Modyfikowanie rekordów odbywa się przy użyciu żądań HTTP PUT. Poza tą zmianą `Edit` Metoda jest niemal identyczna z `Create` . Należy pamiętać, że jeśli rekord nie zostanie znaleziony, `Edit` Akcja zwróci `NotFound` odpowiedź (404).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Aby przetestować przy użyciu programu Poster, Zmień czasownik na wartość PUT. Określ zaktualizowane dane obiektu w treści żądania.

![Konsola programu post z informacjami o UMIESZCZENIU i odpowiedzi](native-mobile-backend/_static/postman-put.png)

Ta metoda zwraca `NoContent` (204) odpowiedź po pomyślnym, aby zapewnić spójność z istniejącym interfejsem API.

### <a name="deleting-items"></a>Usuwanie elementów

Usuwanie rekordów jest realizowane przez wykonywanie żądań usuwania do usługi i przekazywanie identyfikatora elementu do usunięcia. Podobnie jak w przypadku aktualizacji, żądania dla elementów, które nie istnieją, będą otrzymywać `NotFound` odpowiedzi. W przeciwnym razie pomyślne żądanie otrzyma `NoContent` odpowiedź (204).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Należy pamiętać, że podczas testowania funkcji usuwania w treści żądania nic nie jest wymagane.

![Konsola Poster pokazująca usuwanie i odpowiedź](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Zapobiegaj za pośrednictwem księgowania

Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt. Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu. Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi. Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku. **DTO** jest używany w tym artykule.

DTO może służyć do:

* Zablokuj nadmierne księgowanie.
* Ukryj właściwości, które nie powinny być wyświetlane dla klientów.
* Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.
* Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone. Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.

Aby zademonstrować podejście DTO, zobacz [zapobieganie nadmiernemu księgowaniu](xref:tutorials/first-web-api#prevent-over-posting)

## <a name="common-web-api-conventions"></a>Konwencje wspólnych interfejsów API sieci Web

Podczas tworzenia usług zaplecza dla aplikacji, należy utworzyć spójny zestaw Konwencji lub zasad służących do obsługi zagadnień związanych z rozwojem. Na przykład w podanej powyżej usłudze żądania dla określonych rekordów, które nie zostały znalezione, dotarły do `NotFound` odpowiedzi, a nie `BadRequest` odpowiedzi. Podobnie polecenia wykonywane do tej usługi, które zostały spełnione w typach powiązanych przez model, są zawsze zaznaczone `ModelState.IsValid` i zwracane `BadRequest` dla nieprawidłowych typów modeli.

Po zidentyfikowaniu wspólnych zasad dla interfejsów API można zwykle hermetyzować je w [filtrze](../mvc/controllers/filters.md). Dowiedz się więcej o [sposobie hermetyzacji wspólnych zasad interfejsu API w aplikacjach ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Xamarin. Forms: uwierzytelnianie usługi sieci Web](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: korzystanie z usługi sieci Web RESTful](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: korzystanie z usług sieci Web REST w aplikacjach platformy Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: Tworzenie interfejsu API sieci Web za pomocą ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
