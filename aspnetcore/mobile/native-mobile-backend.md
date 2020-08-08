---
title: Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core
author: ardalis
description: Dowiedz się, jak utworzyć usługi zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych.
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 75943a15878634568830db74f895653571c54d0f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019433"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

Aplikacje mobilne mogą komunikować się z ASP.NET Core usług zaplecza. Aby uzyskać instrukcje dotyczące łączenia lokalnych usług sieci Web z symulatorami systemu iOS i emulatorami systemu Android, zobacz [nawiązywanie połączenia z lokalnymi usługami sieci Web z symulatorów systemu iOS i emulatorów systemu Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Wyświetlanie lub Pobieranie przykładowego kodu usług wewnętrznej bazy danych](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Przykładowa Natywna aplikacja mobilna

W tym samouczku przedstawiono sposób tworzenia usług zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych. Korzysta ona z [aplikacji Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako klienta natywnego, w tym oddzielnych klientów natywnych dla urządzeń z systemami Android, iOS, uniwersalnym i Windows Phone. Aby utworzyć aplikację natywną (i zainstalować niezbędne bezpłatne narzędzia Xamarin), możesz skorzystać z połączonego samouczka, a także pobrać przykładowe rozwiązanie Xamarin. Przykład platformy Xamarin zawiera projekt usług ASP.NET Web API 2, który zastąpi aplikacja ASP.NET Core w tym artykule (bez zmian wymaganych przez klienta).

![Aby aplikacja REST działała na urządzeniu smartphone z systemem Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Funkcje

Aplikacja ToDoRest obsługuje wyświetlanie listy, Dodawanie, usuwanie i aktualizowanie elementów do wykonania. Każdy element ma identyfikator, nazwę, notatki i Właściwość wskazującą, czy została jeszcze ukończona.

Główny widok elementów, jak pokazano powyżej, zawiera listę nazw poszczególnych elementów i wskazuje, czy jest on wykonany za pomocą znacznika wyboru.

Naciśnięcie `+` ikony powoduje otwarcie okna dialogowego Dodawanie elementu:

![Okno dialogowe Dodawanie elementu](native-mobile-backend/_static/todo-android-new-item.png)

Naciśnięcie elementu na głównym ekranie listy otwiera okno dialogowe edytowania, w którym można modyfikować nazwę elementu, notatki i gotowe ustawienia lub można usunąć element:

![Edytowanie elementu — okno dialogowe](native-mobile-backend/_static/todo-android-edit-item.png)

Ten przykład jest domyślnie skonfigurowany do korzystania z usług zaplecza hostowanych w developer.xamarin.com, które zezwalają na operacje tylko do odczytu. Aby przetestować samodzielnie aplikację ASP.NET Core utworzoną w następnej sekcji działającej na komputerze, musisz zaktualizować `RestUrl` stałą aplikacji. Przejdź do `ToDoREST` projektu i Otwórz plik *Constants.cs* . Zastąp ciąg `RestUrl` adresem URL, który zawiera adres IP maszyny (nie localhost lub 127.0.0.1, ponieważ ten adres jest używany z emulatora urządzenia, a nie z komputera). Uwzględnij również numer portu (5000). Aby sprawdzić, czy usługi działają z urządzeniem, upewnij się, że nie masz aktywnej zapory, która blokuje dostęp do tego portu.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Tworzenie projektu ASP.NET Core

Utwórz nową aplikację sieci Web ASP.NET Core w programie Visual Studio. Wybierz szablon internetowego interfejsu API i bez uwierzytelniania. Nazwij projekt *ToDoApi*.

![Okno dialogowe Nowa aplikacja sieci Web ASP.NET z wybranym szablonem projektu interfejsu API sieci Web](native-mobile-backend/_static/web-api-template.png)

Aplikacja powinna odpowiedzieć na wszystkie żądania kierowane do portu 5000. Zaktualizuj *program.cs* do dołączenia `.UseUrls("http://*:5000")` , aby to osiągnąć:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Upewnij się, że aplikacja jest uruchamiana bezpośrednio, a nie w IIS Express, co domyślnie ignoruje żądania nielokalne. Uruchom polecenie [dotnet Run](/dotnet/core/tools/dotnet-run) z wiersza polecenia lub wybierz profil nazwy aplikacji z listy rozwijanej cel debugowania na pasku narzędzi programu Visual Studio.

Dodaj klasę modelu do reprezentowania elementów do wykonania. Oznacz wymagane pola `[Required]` atrybutem:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Metody interfejsu API wymagają pewnego sposobu pracy z danymi. Użyj tego samego `IToDoRepository` interfejsu, z którego korzysta oryginalny przykład platformy Xamarin:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

Dla tego przykładu implementacja używa tylko prywatnej kolekcji elementów:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Skonfiguruj implementację w programie *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

W tym momencie można przystąpić do tworzenia *ToDoItemsController*.

> [!TIP]
> Dowiedz się więcej o tworzeniu interfejsów API sieci Web w temacie Tworzenie [pierwszego internetowego interfejsu API za pomocą ASP.NET Core MVC i Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Tworzenie kontrolera

Dodaj nowy kontroler do projektu, *ToDoItemsController*. Powinien on dziedziczyć po elemencie Microsoft. AspNetCore. MVC. Controller. Dodaj `Route` atrybut, aby wskazać, że kontroler będzie obsługiwać żądania wysyłane do ścieżek zaczynających się od `api/todoitems` . `[controller]`Token w marszrucie jest zastępowany nazwą kontrolera (z pominięciem `Controller` sufiksu) i jest szczególnie przydatny w przypadku tras globalnych. Dowiedz się więcej o [routingu](../fundamentals/routing.md).

Kontroler wymaga `IToDoRepository` funkcji do; Zażądaj wystąpienia tego typu za pomocą konstruktora kontrolera. W czasie wykonywania to wystąpienie zostanie dostarczone przy użyciu obsługi platformy w celu [iniekcji zależności](../fundamentals/dependency-injection.md).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Ten interfejs API obsługuje cztery różne czasowniki HTTP do wykonywania operacji CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie) w źródle danych. Najprostszą z nich jest operacja odczytu, która odnosi się do żądania HTTP GET.

### <a name="reading-items"></a>Odczytywanie elementów

Żądanie listy elementów jest wykonywane z żądaniem GET do `List` metody. `[HttpGet]`Atrybut `List` metody wskazuje, że ta akcja powinna obsługiwać tylko żądania GET. Trasa dla tej akcji jest trasą określoną na kontrolerze. Nie trzeba koniecznie używać nazwy akcji jako części trasy. Wystarczy upewnić się, że każda akcja ma unikatową i jednoznaczną trasę. Atrybuty routingu mogą być stosowane zarówno na poziomie kontrolera, jak i metody do tworzenia określonych tras.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

`List`Metoda zwraca kod odpowiedzi 200 OK i wszystkie elementy do wykonania, zserializowane jako JSON.

Nową metodę interfejsu API można testować przy użyciu różnych narzędzi, takich jak program [Poster](https://www.getpostman.com/docs/), jak pokazano tutaj:

![Konsola programu Poster pokazująca żądanie GET dla TodoItems i treść odpowiedzi przedstawiającą kod JSON dla trzech zwróconych elementów](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Tworzenie elementów

Zgodnie z Konwencją tworzenie nowych elementów danych jest mapowane na zlecenie HTTP POST. `Create` `[HttpPost]` Do metody zastosowano atrybut i zaakceptowano `ToDoItem` wystąpienie. Ponieważ `item` argument jest przesyłany w treści wpisu, ten parametr określa `[FromBody]` atrybut.

Wewnątrz metody element jest sprawdzany pod kątem ważności i wcześniejszej istnienia w magazynie danych, a jeśli nie wystąpią żadne problemy, zostanie dodany za pomocą repozytorium. Sprawdzanie `ModelState.IsValid` sprawdza [poprawność modelu](../mvc/models/validation.md)i należy je wykonać w każdej metodzie interfejsu API, która akceptuje dane wejściowe użytkownika.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Przykład używa wyliczenia zawierającego kody błędów, które są przesyłane do klienta mobilnego:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Przetestuj Dodawanie nowych elementów przy użyciu programu Poster, wybierając pozycję POST Verb dostarczającą nowy obiekt w formacie JSON w treści żądania. Należy również dodać nagłówek żądania określający `Content-Type` `application/json` .

![Konsola programu Poster pokazująca wpis i odpowiedź](native-mobile-backend/_static/postman-post.png)

Metoda zwraca nowo utworzony element w odpowiedzi.

### <a name="updating-items"></a>Aktualizowanie elementów

Modyfikowanie rekordów odbywa się przy użyciu żądań HTTP PUT. Poza tą zmianą `Edit` Metoda jest niemal identyczna z `Create` . Należy pamiętać, że jeśli rekord nie zostanie znaleziony, `Edit` Akcja zwróci `NotFound` odpowiedź (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Aby przetestować przy użyciu programu Poster, Zmień czasownik na wartość PUT. Określ zaktualizowane dane obiektu w treści żądania.

![Konsola programu post z informacjami o UMIESZCZENIU i odpowiedzi](native-mobile-backend/_static/postman-put.png)

Ta metoda zwraca `NoContent` (204) odpowiedź po pomyślnym, aby zapewnić spójność z istniejącym interfejsem API.

### <a name="deleting-items"></a>Usuwanie elementów

Usuwanie rekordów jest realizowane przez wykonywanie żądań usuwania do usługi i przekazywanie identyfikatora elementu do usunięcia. Podobnie jak w przypadku aktualizacji, żądania dla elementów, które nie istnieją, będą otrzymywać `NotFound` odpowiedzi. W przeciwnym razie pomyślne żądanie otrzyma `NoContent` odpowiedź (204).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Należy pamiętać, że podczas testowania funkcji usuwania w treści żądania nic nie jest wymagane.

![Konsola Poster pokazująca usuwanie i odpowiedź](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Konwencje wspólnych interfejsów API sieci Web

Podczas tworzenia usług zaplecza dla aplikacji, należy utworzyć spójny zestaw Konwencji lub zasad służących do obsługi zagadnień związanych z rozwojem. Na przykład w podanej powyżej usłudze żądania dla określonych rekordów, które nie zostały znalezione, dotarły do `NotFound` odpowiedzi, a nie `BadRequest` odpowiedzi. Podobnie polecenia wykonywane do tej usługi, które zostały spełnione w typach powiązanych przez model, są zawsze zaznaczone `ModelState.IsValid` i zwracane `BadRequest` dla nieprawidłowych typów modeli.

Po zidentyfikowaniu wspólnych zasad dla interfejsów API można zwykle hermetyzować je w [filtrze](../mvc/controllers/filters.md). Dowiedz się więcej o [sposobie hermetyzacji wspólnych zasad interfejsu API w aplikacjach ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Uwierzytelnianie i autoryzacja](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
