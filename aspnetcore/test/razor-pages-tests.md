---
title: Testy jednostkowe brzytwy w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak tworzyć testy jednostkowe dla aplikacji Razor Pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664410"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Testy jednostkowe brzytwy w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe aplikacji Razor Pages. Testy warstwy dostępu do danych (DAL) i modeli stron pomagają zapewnić:

* Części aplikacji Razor Pages działają niezależnie i razem jako jednostka podczas budowy aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy spowodowane przez aktualizacje kodu, znajdują się podczas automatycznego tworzenia i wdrażania.

W tym temacie przyjęto założenie, że masz podstawową wiedzę na temat aplikacji Razor Pages i testów jednostkowych. Jeśli nie znasz aplikacji Razor Pages lub pojęć testowych, zapoznaj się z następującymi tematami:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie jednostkowe języka C# w rdzeniu .NET przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *src/RazorPagesTestSample*         | Umożliwia użytkownikowi dodawanie wiadomości, usuwanie jednej wiadomości, usuwanie wszystkich wiadomości i analizowanie wiadomości (znajdowanie średniej liczby słów na wiadomość). |
| Aplikacja testowa    | *testy/RazorPagesTestSample.Testy* | Służy do jednostkowego testowania modelu strony DAL i Indeks aplikacji wiadomości. |

Testy można uruchomić przy użyciu wbudowanych funkcji testowych IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub Visual Studio [dla komputerów Mac.](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Jeśli używasz [programu Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia, wykonaj następujące polecenie w wierszu polecenia w folderze *tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji wiadomości

Aplikacja wiadomości to system wiadomości Razor Pages o następujących cechach:

* Strona Indeks aplikacji *(Pages/Index.cshtml* i *Pages/Index.cshtml.cs)* udostępnia metody interfejsu użytkownika i modelu strony w celu kontrolowania dodawania, usuwania i analizy wiadomości (znajdź średnią liczbę słów na wiadomość).
* Komunikat jest opisany przez `Message` klasę *(Data/Message.cs)* z `Id` dwiema właściwościami: (key) i `Text` (message). Obiekt `Text` jest wymagany i ograniczony do 200 znaków.
* Wiadomości są przechowywane przy użyciu [bazy danych w pamięci encji](/ef/core/providers/in-memory/)&#8224;.
* Aplikacja zawiera dal w klasie kontekstu bazy danych ( `AppDbContext` *Data/AppDbContext.cs*). Metody DAL są `virtual`oznaczone , co pozwala na szydzenie z metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn wiadomości jest inicjowany z trzema wiadomościami. Te *rozstawione wiadomości* są również używane w testach.

&#8224;temat EF, [Test z InMemory](/ef/core/miscellaneous/testing/in-memory), wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie użyto struktury testów [xUnit.](https://xunit.github.io/) Pojęcia testowe i implementacje testów w różnych strukturach testów są podobne, ale nie identyczne.

Chociaż przykładowa aplikacja nie używa wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz Projektowanie <xref:mvc/controllers/testing> [warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testowanie organizacji aplikacji

Aplikacja testowa jest aplikacją konsoli wewnątrz *folderu tests/RazorPagesTestSample.Tests.*

| Testowanie folderu aplikacji | Opis |
| --------------- | ----------- |
| *Testy jednostek*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla warstwy DAL.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe modelu strony indeksu.</li></ul> |
| *Narzędzia*     | Zawiera `TestDbContextOptions` metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego warstwy DAL, tak aby baza danych została zresetowana do stanu linii bazowej dla każdego testu. |

Struktura testowa to [xUnit](https://xunit.github.io/). Struktura szydercza obiektu jest [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma dal z czterech metod `AppDbContext` zawartych w klasie (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| Metoda WARSTWY DAL               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje z `List<Message>` bazy danych posortowane przez `Text` właściwość. |
| `AddMessageAsync`        | Dodaje `Message` do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa pojedynczy `Message` z bazy `Id`danych przez .                      |

Testy jednostkowe dal <xref:Microsoft.EntityFrameworkCore.DbContextOptions> wymagają podczas `AppDbContext` tworzenia nowego dla każdego testu. Jednym z podejść `DbContextOptions` do tworzenia dla <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>każdego testu jest użycie:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem jest, że każdy test odbiera bazy danych w dowolnym stanie poprzedniego testu opuścił go. Może to być problematyczne podczas próby zapisu testów jednostek atomowych, które nie kolidują ze sobą. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych `DbContextOptions` dla każdego testu, podaj wystąpienie oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak `Utilities` to `TestDbContextOptions` zrobić przy użyciu metody klasy (*testy/RazorPagesTestSample.Tests/Utilities/Utilities.cs):*

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Za `DbContextOptions` pomocą w testach jednostkowych dal umożliwia każdy test do uruchomienia niepodzielnie z wystąpienia świeżej bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa `DataAccessLayerTest` w klasie (*UnitTests/DataAccessLayerTest.cs*) jest zgodna z podobnym wzorcem Arrange-Act-Assert:

1. Rozmieść: Baza danych jest skonfigurowana dla testu i/lub jest zdefiniowany oczekiwany wynik.
1. Akt: Test jest wykonywany.
1. Assert: Potwierdzenia są do określenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` metoda jest odpowiedzialna za usunięcie pojedynczej `Id` wiadomości zidentyfikowanej przez jego (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa wiadomość, gdy wiadomość jest obecna w bazie danych. Inna metoda testuje, że baza danych `Id` nie zmienia się, jeśli komunikat o usunięciu nie istnieje. Metoda `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` jest przedstawiona poniżej:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Po pierwsze, metoda wykonuje Krok Rozmieść, gdzie odbywa się przygotowanie do kroku Act. Wiadomości wysiewu `seedMessages`są uzyskiwane i przechowywane w pliku . Wiadomości wysiewu są zapisywane w bazie danych. Wiadomość z `Id` of `1` jest ustawiona do usunięcia. Po `DeleteMessageAsync` wykonaniu metody oczekiwane komunikaty powinny mieć wszystkie komunikaty `Id` z `1`wyjątkiem tej z . Zmienna `expectedMessages` reprezentuje ten oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: `DeleteMessageAsync` Metoda jest wykonywana `recId` przechodząc `1`w:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` z kontekstu i porównuje `expectedMessages` go do twierdząc, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, `List<Message>` że te dwa są takie same:

* Wiadomości są uporządkowane `Id`według .
* Pary wiadomości są porównywane na `Text` właściwości.

Podobna metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` testowa sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny `DeleteMessageAsync` być równe rzeczywistym komunikatom po wykonaniu metody. Nie powinno być żadnych zmian w zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron Indeks znajdują `IndexModel` się w klasie w *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z warstwy DAL dla `GetMessagesAsync` interfejsu użytkownika przy użyciu metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, wywołania, `AddMessageAsync` aby dodać wiadomość do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołania, `DeleteAllMessagesAsync` aby usunąć wszystkie wiadomości w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje, `DeleteMessageAsync` aby usunąć wiadomość `Id` z określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli jedna lub więcej wiadomości znajduje się w bazie danych, oblicza średnią liczbę słów na wiadomość. |

Metody modelu strony są testowane przy `IndexPageTests` użyciu siedmiu testów w klasie (*testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Testy używają znanego wzorca Arrange-Assert-Act. Testy te koncentrują się na:

* Określanie, czy metody postępuj zgodnie z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowy.
* Potwierdzenie, że metody <xref:Microsoft.AspNetCore.Mvc.IActionResult>dają prawidłowe .
* Sprawdzanie, czy przypisania wartości właściwości są dokonywane poprawnie.

Ta grupa testów często mock metody dal do produkcji oczekiwanych danych dla kroku act, gdzie metoda modelu strony jest wykonywana. Na przykład `GetMessagesAsync` metoda `AppDbContext` jest wyśmiewany do produkcji danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, makieta zwraca wynik. Dane nie pochodzą z bazy danych. Tworzy to przewidywalne, niezawodne warunki testowe dla korzystania z warstwy DAL w testach modelu strony.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` pokazuje, `GetMessagesAsync` jak metoda jest wyśmiewany dla modelu strony:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy `OnGetAsync` metoda jest wykonywana w kroku Act, wywołuje `GetMessagesAsync` metodę modelu strony.

Krok ustawy o teście jednostkowym (*testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs):*

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu strony (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` w warstwie DAL nie zwraca wynik dla tej metody wywołania. Wyśmiewana wersja metody zwraca wynik.

W `Assert` kroku rzeczywiste wiadomości`actualMessages`( ) są `Messages` przypisywane z właściwości modelu strony. Sprawdzanie typu jest również wykonywane podczas przypisywania wiadomości. Oczekiwane i rzeczywiste wiadomości `Text` są porównywane przez ich właściwości. Test potwierdza, że `List<Message>` dwa wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w tej grupie tworzą <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>obiekty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelu <xref:Microsoft.AspNetCore.Mvc.ActionContext> strony, `PageContext`które `ViewDataDictionary`obejmują , `PageContext`, an, aby ustanowić , a i . Są one przydatne w przeprowadzaniu testów. Na przykład aplikacja wiadomości ustanawia `ModelState` błąd <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> z aby <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> sprawdzić, `OnPostAddMessageAsync` czy prawidłowa jest zwracana podczas wykonywania:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Testowanie jednostkowe języka C# w rdzeniu .NET przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Jednostka testować kod](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do xUnit.net: Korzystanie z programu .NET Core z wierszem polecenia SDK .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Szybki start moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje testy jednostkowe aplikacji Razor Pages. Testy warstwy dostępu do danych (DAL) i modeli stron pomagają zapewnić:

* Części aplikacji Razor Pages działają niezależnie i razem jako jednostka podczas budowy aplikacji.
* Klasy i metody mają ograniczone zakresy odpowiedzialności.
* Istnieje dodatkowa dokumentacja dotycząca zachowania aplikacji.
* Regresje, które są błędy spowodowane przez aktualizacje kodu, znajdują się podczas automatycznego tworzenia i wdrażania.

W tym temacie przyjęto założenie, że masz podstawową wiedzę na temat aplikacji Razor Pages i testów jednostkowych. Jeśli nie znasz aplikacji Razor Pages lub pojęć testowych, zapoznaj się z następującymi tematami:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Testowanie jednostkowe języka C# w rdzeniu .NET przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowy projekt składa się z dwóch aplikacji:

| Aplikacja         | Folder projektu                     | Opis |
| ----------- | ---------------------------------- | ----------- |
| Aplikacja wiadomości | *src/RazorPagesTestSample*         | Umożliwia użytkownikowi dodawanie wiadomości, usuwanie jednej wiadomości, usuwanie wszystkich wiadomości i analizowanie wiadomości (znajdowanie średniej liczby słów na wiadomość). |
| Aplikacja testowa    | *testy/RazorPagesTestSample.Testy* | Służy do jednostkowego testowania modelu strony DAL i Indeks aplikacji wiadomości. |

Testy można uruchomić przy użyciu wbudowanych funkcji testowych IDE, takich jak [Visual Studio](/visualstudio/test/unit-test-your-code) lub Visual Studio [dla komputerów Mac.](/dotnet/core/tutorials/using-on-mac-vs-full-solution) Jeśli używasz [programu Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia, wykonaj następujące polecenie w wierszu polecenia w folderze *tests/RazorPagesTestSample.Tests:*

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organizacja aplikacji wiadomości

Aplikacja wiadomości to system wiadomości Razor Pages o następujących cechach:

* Strona Indeks aplikacji *(Pages/Index.cshtml* i *Pages/Index.cshtml.cs)* udostępnia metody interfejsu użytkownika i modelu strony w celu kontrolowania dodawania, usuwania i analizy wiadomości (znajdź średnią liczbę słów na wiadomość).
* Komunikat jest opisany przez `Message` klasę *(Data/Message.cs)* z `Id` dwiema właściwościami: (key) i `Text` (message). Obiekt `Text` jest wymagany i ograniczony do 200 znaków.
* Wiadomości są przechowywane przy użyciu [bazy danych w pamięci encji](/ef/core/providers/in-memory/)&#8224;.
* Aplikacja zawiera dal w klasie kontekstu bazy danych ( `AppDbContext` *Data/AppDbContext.cs*). Metody DAL są `virtual`oznaczone , co pozwala na szydzenie z metod do użycia w testach.
* Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn wiadomości jest inicjowany z trzema wiadomościami. Te *rozstawione wiadomości* są również używane w testach.

&#8224;temat EF, [Test z InMemory](/ef/core/miscellaneous/testing/in-memory), wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest. W tym temacie użyto struktury testów [xUnit.](https://xunit.github.io/) Pojęcia testowe i implementacje testów w różnych strukturach testów są podobne, ale nie identyczne.

Chociaż przykładowa aplikacja nie używa wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (UoW),](https://martinfowler.com/eaaCatalog/unitOfWork.html)Razor Pages obsługuje te wzorce rozwoju. Aby uzyskać więcej informacji, zobacz Projektowanie <xref:mvc/controllers/testing> [warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i (przykład implementuje wzorzec repozytorium).

## <a name="test-app-organization"></a>Testowanie organizacji aplikacji

Aplikacja testowa jest aplikacją konsoli wewnątrz *folderu tests/RazorPagesTestSample.Tests.*

| Testowanie folderu aplikacji | Opis |
| --------------- | ----------- |
| *Testy jednostek*     | <ul><li>*DataAccessLayerTest.cs* zawiera testy jednostkowe dla warstwy DAL.</li><li>*IndexPageTests.cs* zawiera testy jednostkowe modelu strony indeksu.</li></ul> |
| *Narzędzia*     | Zawiera `TestDbContextOptions` metodę używaną do tworzenia nowych opcji kontekstu bazy danych dla każdego testu jednostkowego warstwy DAL, tak aby baza danych została zresetowana do stanu linii bazowej dla każdego testu. |

Struktura testowa to [xUnit](https://xunit.github.io/). Struktura szydercza obiektu jest [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Testy jednostkowe warstwy dostępu do danych (DAL)

Aplikacja wiadomości ma dal z czterech metod `AppDbContext` zawartych w klasie (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Każda metoda ma jeden lub dwa testy jednostkowe w aplikacji testowej.

| Metoda WARSTWY DAL               | Funkcja                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Uzyskuje z `List<Message>` bazy danych posortowane przez `Text` właściwość. |
| `AddMessageAsync`        | Dodaje `Message` do bazy danych.                                          |
| `DeleteAllMessagesAsync` | Usuwa wszystkie `Message` wpisy z bazy danych.                           |
| `DeleteMessageAsync`     | Usuwa pojedynczy `Message` z bazy `Id`danych przez .                      |

Testy jednostkowe dal <xref:Microsoft.EntityFrameworkCore.DbContextOptions> wymagają podczas `AppDbContext` tworzenia nowego dla każdego testu. Jednym z podejść `DbContextOptions` do tworzenia dla <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>każdego testu jest użycie:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Problem z tym podejściem jest, że każdy test odbiera bazy danych w dowolnym stanie poprzedniego testu opuścił go. Może to być problematyczne podczas próby zapisu testów jednostek atomowych, które nie kolidują ze sobą. Aby wymusić `AppDbContext` użycie nowego kontekstu bazy danych `DbContextOptions` dla każdego testu, podaj wystąpienie oparte na nowym dostawcy usług. Aplikacja testowa pokazuje, jak `Utilities` to `TestDbContextOptions` zrobić przy użyciu metody klasy (*testy/RazorPagesTestSample.Tests/Utilities/Utilities.cs):*

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Za `DbContextOptions` pomocą w testach jednostkowych dal umożliwia każdy test do uruchomienia niepodzielnie z wystąpienia świeżej bazy danych:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Każda metoda testowa `DataAccessLayerTest` w klasie (*UnitTests/DataAccessLayerTest.cs*) jest zgodna z podobnym wzorcem Arrange-Act-Assert:

1. Rozmieść: Baza danych jest skonfigurowana dla testu i/lub jest zdefiniowany oczekiwany wynik.
1. Akt: Test jest wykonywany.
1. Assert: Potwierdzenia są do określenia, czy wynik testu jest sukcesem.

Na przykład `DeleteMessageAsync` metoda jest odpowiedzialna za usunięcie pojedynczej `Id` wiadomości zidentyfikowanej przez jego (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Istnieją dwa testy dla tej metody. Jeden test sprawdza, czy metoda usuwa wiadomość, gdy wiadomość jest obecna w bazie danych. Inna metoda testuje, że baza danych `Id` nie zmienia się, jeśli komunikat o usunięciu nie istnieje. Metoda `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` jest przedstawiona poniżej:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Po pierwsze, metoda wykonuje Krok Rozmieść, gdzie odbywa się przygotowanie do kroku Act. Wiadomości wysiewu `seedMessages`są uzyskiwane i przechowywane w pliku . Wiadomości wysiewu są zapisywane w bazie danych. Wiadomość z `Id` of `1` jest ustawiona do usunięcia. Po `DeleteMessageAsync` wykonaniu metody oczekiwane komunikaty powinny mieć wszystkie komunikaty `Id` z `1`wyjątkiem tej z . Zmienna `expectedMessages` reprezentuje ten oczekiwany wynik.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Metoda działa: `DeleteMessageAsync` Metoda jest wykonywana `recId` przechodząc `1`w:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Na koniec metoda uzyskuje `Messages` z kontekstu i porównuje `expectedMessages` go do twierdząc, że dwa są równe:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

W celu porównania, `List<Message>` że te dwa są takie same:

* Wiadomości są uporządkowane `Id`według .
* Pary wiadomości są porównywane na `Text` właściwości.

Podobna metoda `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` testowa sprawdza wynik próby usunięcia wiadomości, która nie istnieje. W takim przypadku oczekiwane komunikaty w bazie danych powinny `DeleteMessageAsync` być równe rzeczywistym komunikatom po wykonaniu metody. Nie powinno być żadnych zmian w zawartości bazy danych:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Testy jednostkowe metod modelu strony

Inny zestaw testów jednostkowych jest odpowiedzialny za testy metod modelu strony. W aplikacji wiadomości modele stron Indeks znajdują `IndexModel` się w klasie w *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Metoda modelu strony | Funkcja |
| ----------------- | -------- |
| `OnGetAsync` | Uzyskuje komunikaty z warstwy DAL dla `GetMessagesAsync` interfejsu użytkownika przy użyciu metody. |
| `OnPostAddMessageAsync` | Jeśli [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest prawidłowy, wywołania, `AddMessageAsync` aby dodać wiadomość do bazy danych. |
| `OnPostDeleteAllMessagesAsync` | Wywołania, `DeleteAllMessagesAsync` aby usunąć wszystkie wiadomości w bazie danych. |
| `OnPostDeleteMessageAsync` | Wykonuje, `DeleteMessageAsync` aby usunąć wiadomość `Id` z określonym. |
| `OnPostAnalyzeMessagesAsync` | Jeśli jedna lub więcej wiadomości znajduje się w bazie danych, oblicza średnią liczbę słów na wiadomość. |

Metody modelu strony są testowane przy `IndexPageTests` użyciu siedmiu testów w klasie (*testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Testy używają znanego wzorca Arrange-Assert-Act. Testy te koncentrują się na:

* Określanie, czy metody postępuj zgodnie z prawidłowym zachowaniem, gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowy.
* Potwierdzenie, że metody <xref:Microsoft.AspNetCore.Mvc.IActionResult>dają prawidłowe .
* Sprawdzanie, czy przypisania wartości właściwości są dokonywane poprawnie.

Ta grupa testów często mock metody dal do produkcji oczekiwanych danych dla kroku act, gdzie metoda modelu strony jest wykonywana. Na przykład `GetMessagesAsync` metoda `AppDbContext` jest wyśmiewany do produkcji danych wyjściowych. Gdy metoda modelu strony wykonuje tę metodę, makieta zwraca wynik. Dane nie pochodzą z bazy danych. Tworzy to przewidywalne, niezawodne warunki testowe dla korzystania z warstwy DAL w testach modelu strony.

Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` pokazuje, `GetMessagesAsync` jak metoda jest wyśmiewany dla modelu strony:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Gdy `OnGetAsync` metoda jest wykonywana w kroku Act, wywołuje `GetMessagesAsync` metodę modelu strony.

Krok ustawy o teście jednostkowym (*testy/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs):*

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage``OnGetAsync` metoda modelu strony (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Metoda `GetMessagesAsync` w warstwie DAL nie zwraca wynik dla tej metody wywołania. Wyśmiewana wersja metody zwraca wynik.

W `Assert` kroku rzeczywiste wiadomości`actualMessages`( ) są `Messages` przypisywane z właściwości modelu strony. Sprawdzanie typu jest również wykonywane podczas przypisywania wiadomości. Oczekiwane i rzeczywiste wiadomości `Text` są porównywane przez ich właściwości. Test potwierdza, że `List<Message>` dwa wystąpienia zawierają te same komunikaty.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Inne testy w tej grupie tworzą <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>obiekty <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>modelu <xref:Microsoft.AspNetCore.Mvc.ActionContext> strony, `PageContext`które `ViewDataDictionary`obejmują , `PageContext`, an, aby ustanowić , a i . Są one przydatne w przeprowadzaniu testów. Na przykład aplikacja wiadomości ustanawia `ModelState` błąd <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> z aby <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> sprawdzić, `OnPostAddMessageAsync` czy prawidłowa jest zwracana podczas wykonywania:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Testowanie jednostkowe języka C# w rdzeniu .NET przy użyciu testu dotnet i xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Jednostka testować kod](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Wprowadzenie do xUnit.net: Korzystanie z programu .NET Core z wierszem polecenia SDK .NET](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Szybki start moq](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
