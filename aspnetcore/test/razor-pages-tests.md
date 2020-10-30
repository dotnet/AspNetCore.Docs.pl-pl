---
title: Razor Testy jednostkowe stron w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć testy jednostkowe dla Razor aplikacji stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058041"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>Razor Testy jednostkowe stron w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe Razor aplikacji stron. Testy warstwy dostępu do danych (DAL) i modele stron pomagają zapewnić:

* Części Razor aplikacji stron działają niezależnie i razem jako jednostki podczas konstruowania aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy związane z aktualizacjami kodu, są dostępne podczas zautomatyzowanego kompilowania i wdrażania.

W tym temacie założono, że masz podstawową wiedzę na temat Razor aplikacji i testów jednostkowych. Jeśli nie masz doświadczenia w korzystaniu ze Razor stron aplikacji lub testów, zobacz następujące tematy:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie jednostkowe w języku C# w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *SRC/ Razor PagesTestSample*         | Zezwala użytkownikowi na dodawanie wiadomości, usuwanie jednego komunikatu, usuwanie wszystkich komunikatów i analizowanie komunikatów (Znajdowanie średniej liczby wyrazów na komunikat). |
| Aplikacja testowa    | *testy/ Razor PagesTestSample. Tests* | Służy do przetestowania jednostki DAL i indeksu strony aplikacji wiadomości. |

Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub [Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w folderze *Tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji komunikatów

Aplikacja wiadomości jest Razor systemem komunikatów stron o następujących cechach:

* Strona indeks aplikacji ( *Pages/index. cshtml* i *Pages/index. cshtml. cs* ) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (Znajdź średnią liczbę wyrazów na komunikat).
* Komunikat jest opisywany przez `Message` klasę ( *Data/Message. cs* ) z dwiema właściwościami: `Id` (Key) i `Text` (Message). `Text`Właściwość jest wymagana i jest ograniczona do 200 znaków.
* Komunikaty są przechowywane przy użyciu&#8224; [bazy danych Entity Framework w pamięci](/ef/core/providers/in-memory/) .
* Aplikacja zawiera DAL w swojej klasie kontekstu bazy danych `AppDbContext` ( *Data/AppDbContext. cs* ). Metody DAL są oznaczone `virtual` , co umożliwia imitację metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów. Te *rozsiane komunikaty* są również używane w testach.

&#8224;temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory)— wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) . Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.

Chociaż Przykładowa aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (pracownicy)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor strony programu obsługują te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i <xref:mvc/controllers/testing> (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testuj organizację aplikacji

Aplikacja testowa jest aplikacją konsolową wewnątrz folderu *Tests/ Razor PagesTestSample. Tests* .

| Testuj folder aplikacji | Opis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla dal.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe dla modelu strony indeksu.</li></ul> |
| *Narzędzia*     | Zawiera `TestDbContextOptions` metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego dal, aby baza danych była resetowana do jego warunku bazowego dla każdego testu. |

Platforma testowa jest [xUnit](https://xunit.github.io/). Struktura imitacji obiektu jest [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma DAL z czterema metodami zawartymi w `AppDbContext` klasie ( *src/ Razor PagesTestSample/Data/AppDbContext. cs* ). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| DAL, Metoda               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje `List<Message>` z bazy danych posortowanej według `Text` właściwości. |
| `AddMessageAsync`        | Dodaje `Message` do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa jeden `Message` z baz danych przez `Id` .                      |

Testy jednostkowe DAL wymagają <xref:Microsoft.EntityFrameworkCore.DbContextOptions> podczas tworzenia nowego `AppDbContext` dla każdego testu. Jednym z metod tworzenia `DbContextOptions` dla każdego testu jest użycie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem polega na tym, że każdy test otrzymuje bazę danych w dowolnym stanie, w którym został pozostawiony poprzedni test. Może to być przyczyną problemów podczas próby zapisania niepodzielnych testów jednostkowych, które nie zakłócają siebie nawzajem. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych dla każdego testu, podaj `DbContextOptions` wystąpienie, które jest oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak to zrobić za pomocą `Utilities` metody klasy `TestDbContextOptions` ( *Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Użycie `DbContextOptions` w ramach testów jednostkowych dal umożliwia niepodzielne uruchamianie każdego testu w przypadku wystąpienia nowego bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa w `DataAccessLayerTest` klasie ( *UnitTests/DataAccessLayerTest. cs* ) postępuje zgodnie z podobnym wzorcem porządkowania:

1. Porządkowanie: baza danych jest skonfigurowana do testowania i/lub oczekiwany wynik jest zdefiniowany.
1. Działanie: test jest wykonywany.
1. Assert: potwierdzenia są wykonywane w celu ustalenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` Metoda jest odpowiedzialna za usunięcie pojedynczego komunikatu identyfikowanego przez jego `Id` ( *src/ Razor PagesTestSample/Data/AppDbContext. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa komunikat, gdy komunikat jest obecny w bazie danych. Druga metoda testuje, że baza danych nie zmienia się, jeśli komunikat `Id` do usunięcia nie istnieje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Poniżej przedstawiono metodę:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Najpierw Metoda wykonuje krok rozmieszczania, w którym odbywa się przygotowanie do kroku działania. Wypełnianie komunikatów jest uzyskiwane i przechowywane w `seedMessages` . Wypełnianie komunikatów jest zapisywane w bazie danych. Komunikat z programem `Id` `1` jest ustawiany do usunięcia. Gdy `DeleteMessageAsync` Metoda jest wykonywana, oczekiwane komunikaty powinny zawierać wszystkie komunikaty z wyjątkiem jednego z `Id` `1` . `expectedMessages`Zmienna reprezentuje oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: `DeleteMessageAsync` Metoda jest wykonywana w `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` od kontekstu i porównuje ją z `expectedMessages` potwierdzeniem, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, że te dwa `List<Message>` są takie same:

* Wiadomości są uporządkowane według `Id` .
* Pary komunikatów są porównywane z `Text` właściwością.

Podobna metoda testowa `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny być równe rzeczywistym komunikatom po `DeleteMessageAsync` wykonaniu metody. Nie powinno się zmieniać zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron indeksu są dostępne w `IndexModel` klasie w *src/ Razor PagesTestSample/Pages/index. cshtml. cs* .

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z DAL dla interfejsu użytkownika przy użyciu `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, program wywołuje `AddMessageAsync` Dodawanie komunikatu do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołuje `DeleteAllMessagesAsync` usuwanie wszystkich komunikatów w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje `DeleteMessageAsync` , aby usunąć komunikat z `Id` określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli co najmniej jeden komunikat znajduje się w bazie danych, program oblicza średnią liczbę wyrazów na komunikat. |

Metody modelu strony są testowane przy użyciu siedmiu testów w `IndexPageTests` klasie ( *Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs* ). Testy używają znanego wzorca porządkowania i potwierdzeń. Te testy koncentrują się na:

* Określanie, czy metody są zgodne z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowe.
* Potwierdzenie metod daje poprawne <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Sprawdzanie, czy przypisania wartości właściwości są wykonywane poprawnie.

Ta grupa testów często służy do zawzorowania metod DAL, aby generować oczekiwane dane dla kroku działania, w którym jest wykonywana metoda modelu strony. Na przykład `GetMessagesAsync` Metoda `AppDbContext` jest makieta w celu wygenerowania danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, funkcja makieta zwraca wynik. Dane nie pochodzą z bazy danych. Powoduje to utworzenie przewidywalnych, niezawodnych warunków testowania dla korzystania z DAL w testach modelu strony.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test pokazuje, jak `GetMessagesAsync` Metoda jest makietą dla modelu strony:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy `OnGetAsync` Metoda jest wykonywana w kroku Act, wywołuje metodę modelu strony `GetMessagesAsync` .

Krok działania testów jednostkowych ( *testy/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` Metoda modelu strony `OnGetAsync` ( *src/ Razor PagesTestSample/Pages/index. cshtml. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Metoda w dal nie zwraca wyniku dla tego wywołania metody. Makieta wersji metody zwraca wynik.

W tym `Assert` kroku rzeczywiste komunikaty ( `actualMessages` ) są przypisywane z `Messages` właściwości modelu strony. Sprawdzanie typu jest również wykonywane po przypisaniu komunikatów. Oczekiwane i rzeczywiste komunikaty są porównywane według ich `Text` właściwości. Test potwierdza, że dwa `List<Message>` wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w tej grupie umożliwiają tworzenie obiektów modelu strony, które obejmują,, <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ,,,, <xref:Microsoft.AspNetCore.Mvc.ActionContext> Aby określić `PageContext` , `ViewDataDictionary` a i a `PageContext` . Są one przydatne podczas przeprowadzania testów. Na przykład aplikacja wiadomości nawiązuje `ModelState` błąd z programem <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , aby sprawdzić, czy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> po wykonaniu jest zwracany prawidłowy `OnPostAddMessageAsync` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Testowanie jednostkowe w języku C# w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testowanie jednostkowe kodu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do usługi xUnit.net: używanie platformy .NET Core z wierszem polecenia zestawu .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [MOQ — Szybki Start](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe Razor aplikacji stron. Testy warstwy dostępu do danych (DAL) i modele stron pomagają zapewnić:

* Części Razor aplikacji stron działają niezależnie i razem jako jednostki podczas konstruowania aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy związane z aktualizacjami kodu, są dostępne podczas zautomatyzowanego kompilowania i wdrażania.

W tym temacie założono, że masz podstawową wiedzę na temat Razor aplikacji i testów jednostkowych. Jeśli nie masz doświadczenia w korzystaniu ze Razor stron aplikacji lub testów, zobacz następujące tematy:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie jednostkowe w języku C# w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *SRC/ Razor PagesTestSample*         | Zezwala użytkownikowi na dodawanie wiadomości, usuwanie jednego komunikatu, usuwanie wszystkich komunikatów i analizowanie komunikatów (Znajdowanie średniej liczby wyrazów na komunikat). |
| Aplikacja testowa    | *testy/ Razor PagesTestSample. Tests* | Służy do przetestowania jednostki DAL i indeksu strony aplikacji wiadomości. |

Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub [Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w folderze *Tests/ Razor PagesTestSample. Tests* :

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji komunikatów

Aplikacja wiadomości jest Razor systemem komunikatów stron o następujących cechach:

* Strona indeks aplikacji ( *Pages/index. cshtml* i *Pages/index. cshtml. cs* ) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (Znajdź średnią liczbę wyrazów na komunikat).
* Komunikat jest opisywany przez `Message` klasę ( *Data/Message. cs* ) z dwiema właściwościami: `Id` (Key) i `Text` (Message). `Text`Właściwość jest wymagana i jest ograniczona do 200 znaków.
* Komunikaty są przechowywane przy użyciu&#8224; [bazy danych Entity Framework w pamięci](/ef/core/providers/in-memory/) .
* Aplikacja zawiera DAL w swojej klasie kontekstu bazy danych `AppDbContext` ( *Data/AppDbContext. cs* ). Metody DAL są oznaczone `virtual` , co umożliwia imitację metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów. Te *rozsiane komunikaty* są również używane w testach.

&#8224;temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory)— wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) . Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.

Chociaż Przykładowa aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (pracownicy)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor strony programu obsługują te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i <xref:mvc/controllers/testing> (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testuj organizację aplikacji

Aplikacja testowa jest aplikacją konsolową wewnątrz folderu *Tests/ Razor PagesTestSample. Tests* .

| Testuj folder aplikacji | Opis |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla dal.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe dla modelu strony indeksu.</li></ul> |
| *Narzędzia*     | Zawiera `TestDbContextOptions` metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego dal, aby baza danych była resetowana do jego warunku bazowego dla każdego testu. |

Platforma testowa jest [xUnit](https://xunit.github.io/). Struktura imitacji obiektu jest [MOQ](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma DAL z czterema metodami zawartymi w `AppDbContext` klasie ( *src/ Razor PagesTestSample/Data/AppDbContext. cs* ). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| DAL, Metoda               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje `List<Message>` z bazy danych posortowanej według `Text` właściwości. |
| `AddMessageAsync`        | Dodaje `Message` do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa jeden `Message` z baz danych przez `Id` .                      |

Testy jednostkowe DAL wymagają <xref:Microsoft.EntityFrameworkCore.DbContextOptions> podczas tworzenia nowego `AppDbContext` dla każdego testu. Jednym z metod tworzenia `DbContextOptions` dla każdego testu jest użycie <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem polega na tym, że każdy test otrzymuje bazę danych w dowolnym stanie, w którym został pozostawiony poprzedni test. Może to być przyczyną problemów podczas próby zapisania niepodzielnych testów jednostkowych, które nie zakłócają siebie nawzajem. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych dla każdego testu, podaj `DbContextOptions` wystąpienie, które jest oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak to zrobić za pomocą `Utilities` metody klasy `TestDbContextOptions` ( *Tests/ Razor PagesTestSample. Tests/Utilities/Utilities. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Użycie `DbContextOptions` w ramach testów jednostkowych dal umożliwia niepodzielne uruchamianie każdego testu w przypadku wystąpienia nowego bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa w `DataAccessLayerTest` klasie ( *UnitTests/DataAccessLayerTest. cs* ) postępuje zgodnie z podobnym wzorcem porządkowania:

1. Porządkowanie: baza danych jest skonfigurowana do testowania i/lub oczekiwany wynik jest zdefiniowany.
1. Działanie: test jest wykonywany.
1. Assert: potwierdzenia są wykonywane w celu ustalenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` Metoda jest odpowiedzialna za usunięcie pojedynczego komunikatu identyfikowanego przez jego `Id` ( *src/ Razor PagesTestSample/Data/AppDbContext. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa komunikat, gdy komunikat jest obecny w bazie danych. Druga metoda testuje, że baza danych nie zmienia się, jeśli komunikat `Id` do usunięcia nie istnieje. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Poniżej przedstawiono metodę:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Najpierw Metoda wykonuje krok rozmieszczania, w którym odbywa się przygotowanie do kroku działania. Wypełnianie komunikatów jest uzyskiwane i przechowywane w `seedMessages` . Wypełnianie komunikatów jest zapisywane w bazie danych. Komunikat z programem `Id` `1` jest ustawiany do usunięcia. Gdy `DeleteMessageAsync` Metoda jest wykonywana, oczekiwane komunikaty powinny zawierać wszystkie komunikaty z wyjątkiem jednego z `Id` `1` . `expectedMessages`Zmienna reprezentuje oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: `DeleteMessageAsync` Metoda jest wykonywana w `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` od kontekstu i porównuje ją z `expectedMessages` potwierdzeniem, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, że te dwa `List<Message>` są takie same:

* Wiadomości są uporządkowane według `Id` .
* Pary komunikatów są porównywane z `Text` właściwością.

Podobna metoda testowa `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny być równe rzeczywistym komunikatom po `DeleteMessageAsync` wykonaniu metody. Nie powinno się zmieniać zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron indeksu są dostępne w `IndexModel` klasie w *src/ Razor PagesTestSample/Pages/index. cshtml. cs* .

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z DAL dla interfejsu użytkownika przy użyciu `GetMessagesAsync` metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, program wywołuje `AddMessageAsync` Dodawanie komunikatu do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołuje `DeleteAllMessagesAsync` usuwanie wszystkich komunikatów w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje `DeleteMessageAsync` , aby usunąć komunikat z `Id` określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli co najmniej jeden komunikat znajduje się w bazie danych, program oblicza średnią liczbę wyrazów na komunikat. |

Metody modelu strony są testowane przy użyciu siedmiu testów w `IndexPageTests` klasie ( *Tests/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs* ). Testy używają znanego wzorca porządkowania i potwierdzeń. Te testy koncentrują się na:

* Określanie, czy metody są zgodne z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowe.
* Potwierdzenie metod daje poprawne <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Sprawdzanie, czy przypisania wartości właściwości są wykonywane poprawnie.

Ta grupa testów często służy do zawzorowania metod DAL, aby generować oczekiwane dane dla kroku działania, w którym jest wykonywana metoda modelu strony. Na przykład `GetMessagesAsync` Metoda `AppDbContext` jest makieta w celu wygenerowania danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, funkcja makieta zwraca wynik. Dane nie pochodzą z bazy danych. Powoduje to utworzenie przewidywalnych, niezawodnych warunków testowania dla korzystania z DAL w testach modelu strony.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test pokazuje, jak `GetMessagesAsync` Metoda jest makietą dla modelu strony:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy `OnGetAsync` Metoda jest wykonywana w kroku Act, wywołuje metodę modelu strony `GetMessagesAsync` .

Krok działania testów jednostkowych ( *testy/ Razor PagesTestSample. Tests/UnitTests/IndexPageTests. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` Metoda modelu strony `OnGetAsync` ( *src/ Razor PagesTestSample/Pages/index. cshtml. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Metoda w dal nie zwraca wyniku dla tego wywołania metody. Makieta wersji metody zwraca wynik.

W tym `Assert` kroku rzeczywiste komunikaty ( `actualMessages` ) są przypisywane z `Messages` właściwości modelu strony. Sprawdzanie typu jest również wykonywane po przypisaniu komunikatów. Oczekiwane i rzeczywiste komunikaty są porównywane według ich `Text` właściwości. Test potwierdza, że dwa `List<Message>` wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w tej grupie umożliwiają tworzenie obiektów modelu strony, które obejmują,, <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> ,,,, <xref:Microsoft.AspNetCore.Mvc.ActionContext> Aby określić `PageContext` , `ViewDataDictionary` a i a `PageContext` . Są one przydatne podczas przeprowadzania testów. Na przykład aplikacja wiadomości nawiązuje `ModelState` błąd z programem <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , aby sprawdzić, czy <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> po wykonaniu jest zwracany prawidłowy `OnPostAddMessageAsync` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Testowanie jednostkowe w języku C# w programie .NET Core przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Testowanie jednostkowe kodu](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do usługi xUnit.net: używanie platformy .NET Core z wierszem polecenia zestawu .NET SDK](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [MOQ — Szybki Start](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite](https://github.com/telerik/JustMockLite): Struktura dla deweloperów platformy .NET. ( *Niekonserwowane lub obsługiwane przez firmę Microsoft).*

::: moniker-end
