---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRIdentyfikator UID: 

---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Logika kontrolera testów jednostkowych w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

[Testy jednostkowe](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) obejmują testowanie części aplikacji w izolacji z jej infrastruktury i zależności. Podczas logiki kontrolera testów jednostkowych tylko zawartość pojedynczej akcji jest testowana, a nie zachowanie jej zależności lub samego środowiska.

## <a name="unit-testing-controllers"></a>Kontrolery testów jednostkowych

Skonfiguruj testy jednostkowe akcji kontrolera, aby skoncentrować się na zachowaniu kontrolera. Test jednostkowy kontrolera pozwala uniknąć scenariuszy, takich jak [filtry](xref:mvc/controllers/filters), [Routing](xref:fundamentals/routing)i [powiązania modelu](xref:mvc/models/model-binding). Testy, które obejmują interakcje między składnikami, które zbiorczo odpowiadają na żądanie, są obsługiwane przez *testy integracji*. Aby uzyskać więcej informacji na temat testów integracji, zobacz <xref:test/integration-tests> .

Jeśli piszesz filtry niestandardowe i trasy, przetestuj je jednostkowo, a nie jako część testów dla konkretnej akcji kontrolera.

Aby zademonstrować testy jednostkowe kontrolera, przejrzyj następujący kontroler w przykładowej aplikacji. 

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Kontroler Home wyświetla listę sesji burzy mózgów i umożliwia tworzenie nowych sesji burzy mózgów przy użyciu żądania POST:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Poprzedni kontroler:

* Zgodnie z [zasadą jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Oczekuje [iniekcji zależności (di)](xref:fundamentals/dependency-injection) , aby zapewnić wystąpienie `IBrainstormSessionRepository` .
* Program może być testowany z usługą ze makietą `IBrainstormSessionRepository` przy użyciu struktury obiektów imitacji, takiej jak [MOQ](https://www.nuget.org/packages/Moq/). *Obiekt imitacji* jest obiektem prefabrykowanym ze wstępnie określonym zestawem zachowań właściwości i metod używanych do testowania. Aby uzyskać więcej informacji, zobacz [wprowadzenie do testów integracji](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index`Metoda nie ma pętli ani rozgałęziania i wywołuje tylko jedną metodę. Test jednostkowy dla tej akcji:

* Program umożliwia imitację `IBrainstormSessionRepository` usługi przy użyciu `GetTestSessions` metody. `GetTestSessions`tworzy dwie sesje z burzą mózgów z datami i nazwami sesji.
* Wykonuje `Index` metodę.
* Wykonuje potwierdzenia w wyniku zwróconym przez metodę:
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult>Jest zwracany.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) to `StormSessionViewModel` .
  * W programie są przechowywane dwie sesje burzy mózgów `ViewDataDictionary.Model` .

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Testy metody kontrolera macierzystego `HTTP POST Index` sprawdzają, czy:

* Gdy [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) jest `false` , metoda akcja zwraca *400 Nieprawidłowe żądanie* <xref:Microsoft.AspNetCore.Mvc.ViewResult> z odpowiednimi danymi.
* Gdy `ModelState.IsValid` jest `true` :
  * `Add`Metoda w repozytorium jest wywoływana.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>Jest zwracany z prawidłowymi argumentami.

Nieprawidłowy stan modelu jest testowany przez dodanie błędów przy użyciu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , jak pokazano w pierwszym teście poniżej:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowy, jest on `ViewResult` zwracany jako dla żądania GET. Test nie próbuje przekazać nieprawidłowego modelu. Przekazywanie nieprawidłowego modelu nie jest prawidłowym podejściem, ponieważ powiązanie modelu nie jest uruchomione (mimo że [test integracji](xref:test/integration-tests) używa powiązania modelu). W takim przypadku powiązanie modelu nie jest testowane. Te testy jednostkowe służą wyłącznie do testowania kodu w metodzie akcji.

Drugi test weryfikuje, czy gdy `ModelState` jest prawidłowy:

* Dodano nowy element `BrainstormSession` (za pośrednictwem repozytorium).
* Metoda zwraca obiekt `RedirectToActionResult` z oczekiwanymi właściwościami.

Wywołane wywołania, które nie są wywoływane, są zwykle ignorowane, ale wywołanie `Verifiable` na końcu wywołania Instalatora pozwala na weryfikację makiety w teście. Jest to wykonywane z wywołaniem `mockRepo.Verify` , które kończy się niepowodzeniem testu, jeśli oczekiwana Metoda nie została wywołana.

> [!NOTE]
> Biblioteka MOQ używana w tym przykładzie umożliwia mieszanie możliwej do zweryfikowania lub "ścisłych", imitacje z niemożliwymi do zweryfikowania makietami (nazywanymi również "luźnymi" fragmentami lub wycinkami). Dowiedz się więcej o [dostosowywaniu zachowań makiety za pomocą MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) w aplikacji przykładowej wyświetla informacje powiązane z określoną sesją burzy mózgów. Kontroler zawiera logikę do działania z nieprawidłowymi `id` wartościami (Istnieją dwa `return` scenariusze w poniższym przykładzie, aby pokryć te scenariusze). Instrukcja Final `return` zwraca nowy `StormSessionViewModel` Widok (*controllers/SessionController. cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednostkowe obejmują jeden test dla każdego `return` scenariusza w akcji kontrolera sesji `Index` :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Przejście do kontrolera pomysłów powoduje, że aplikacja ujawnia funkcje jako interfejs API sieci Web na `api/ideas` trasie:

* Lista pomysłów ( `IdeaDTO` ) skojarzonych z sesją burzy mózgów jest zwracana przez `ForSession` metodę.
* `Create`Metoda dodaje nowe pomysły do sesji.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Unikaj zwracania jednostek domeny biznesowej bezpośrednio za pośrednictwem wywołań interfejsu API. Jednostki domeny:

* Często zawierają więcej danych niż jest to wymagane przez klienta.
* Niepotrzebne połączenie wewnętrznego modelu domeny aplikacji z publicznie uwidocznionym interfejsem API.

Można wykonać mapowanie między jednostkami domeny a typami zwracanymi do klienta:

* Ręczne korzystanie z programu LINQ `Select` jako aplikacji przykładowej. Aby uzyskać więcej informacji, zobacz [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automatycznie z biblioteką, taką jak [automapowania](https://github.com/AutoMapper/AutoMapper).

Następnie w przykładowej aplikacji przedstawiono testy jednostkowe dla `Create` `ForSession` metod interfejsu API i kontrolera pomysłów.

Przykładowa aplikacja zawiera dwa `ForSession` testy. Pierwszy test określa, czy `ForSession` zwracany jest komunikat <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (nie znaleziono protokołu HTTP) dla nieprawidłowej sesji:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Drugi `ForSession` test określa, czy `ForSession` zwraca listę koncepcji sesji ( `<List<IdeaDTO>>` ) dla prawidłowej sesji. Sprawdza także, czy najpierw należy potwierdzić, że `Name` Właściwość jest poprawna:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Aby przetestować zachowanie `Create` metody `ModelState` , gdy jest nieprawidłowa, przykładowa aplikacja dodaje błąd modelu do kontrolera w ramach testu. Nie próbuj testować walidacji modelu lub powiązania modelu w testach jednostkowych po &mdash; prostu Przetestuj zachowanie metody akcji, gdy wystąpi nieprawidłowe `ModelState` :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Drugi test zależy od `Create` zwracanego repozytorium `null` , więc repozytorium makiety jest skonfigurowane do zwrócenia `null` . Nie ma potrzeby tworzenia testowej bazy danych (w pamięci lub w inny sposób) i konstruowania zapytania zwracającego ten wynik. Test można wykonać w pojedynczej instrukcji, jak przykładowy kod ilustruje:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Trzeci `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession` sprawdza, czy `UpdateAsync` Metoda repozytorium jest wywoływana. Makieta jest wywoływana z `Verifiable` , i wywoływana jest metoda z zastosowanego repozytorium `Verify` w celu potwierdzenia, że metoda możliwa do zweryfikowania jest wykonywana. To nie jest odpowiedzialność za testy jednostkowe, aby upewnić się, że `UpdateAsync` Metoda zapisała dane &mdash; , które można wykonać za pomocą testu integracji.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

W ASP.NET Core 2,1 lub nowszej [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) umożliwia zwrócenie typu pochodnego `ActionResult` lub zwracanego określonego typu.

Przykładowa aplikacja zawiera metodę zwracającą `List<IdeaDTO>` dla danej sesji `id` . Jeśli sesja `id` nie istnieje, kontroler zwraca <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dwa testy `ForSessionActionResult` kontrolera są zawarte w `ApiIdeasControllerTests` .

Pierwszy test potwierdza, że kontroler zwróci `ActionResult` nieistniejącą listę pomysłów dla nieistniejącej sesji `id` :

* `ActionResult`Typ to `ActionResult<List<IdeaDTO>>` .
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>Jest <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> .

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

W przypadku prawidłowej sesji `id` drugi test potwierdza, że metoda zwraca:

* `ActionResult`Z `List<IdeaDTO>` typem.
* [ActionResult \<T> . Wartość](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) jest `List<IdeaDTO>` typem.
* Pierwszy element na liście jest prawidłowym pomysłem pasującym do pomysłu przechowywanego w sesji makiety (uzyskany przez wywołanie `GetTestSession` ).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Przykładowa aplikacja zawiera również metodę tworzenia nowego `Idea` dla danej sesji. Kontroler zwraca:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>dla nieprawidłowego modelu.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Jeśli sesja nie istnieje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>gdy sesja zostanie zaktualizowana przy użyciu nowego pomysłu.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Trzy testy `CreateActionResult` są zawarte w `ApiIdeasControllerTests` .

Pierwszy tekst potwierdza, że <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> jest zwracany dla nieprawidłowego modelu.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Drugi test sprawdza, czy <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> jest zwracany, jeśli sesja nie istnieje.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

W przypadku prawidłowej sesji `id` test końcowy potwierdza, że:

* Metoda zwraca obiekt `ActionResult` z `BrainstormSession` typem.
* [ActionResult \<T> . Wynik](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) to <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult`jest analogiczny do *201 utworzonej* odpowiedzi z `Location` nagłówkiem.
* [ActionResult \<T> . Wartość](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) jest `BrainstormSession` typem.
* Wywołano wywołanie makiety w celu zaktualizowania sesji `UpdateAsync(testSession)` . `Verifiable`Wywołanie metody jest sprawdzane przez wykonanie `mockRepo.Verify()` w potwierdzeniach.
* `Idea`Dla sesji zwracane są dwa obiekty.
* Ostatni element ( `Idea` dodany przez wywołanie makiety do `UpdateAsync` ) jest zgodny z `newIdea` dodaniem do sesji w teście.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Kontrolery](xref:mvc/controllers/actions) odgrywają centralną rolę w dowolnej aplikacji ASP.NET Core MVC. W związku z tym należy mieć pewność, że kontrolery zachowują się zgodnie z oczekiwaniami. Testy automatyczne mogą wykrywać błędy, zanim aplikacja zostanie wdrożona w środowisku produkcyjnym.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Testy jednostkowe logiki kontrolera

[Testy jednostkowe](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) obejmują testowanie części aplikacji w izolacji z jej infrastruktury i zależności. Podczas logiki kontrolera testów jednostkowych tylko zawartość pojedynczej akcji jest testowana, a nie zachowanie jej zależności lub samego środowiska.

Skonfiguruj testy jednostkowe akcji kontrolera, aby skoncentrować się na zachowaniu kontrolera. Test jednostkowy kontrolera pozwala uniknąć scenariuszy, takich jak [filtry](xref:mvc/controllers/filters), [Routing](xref:fundamentals/routing)i [powiązania modelu](xref:mvc/models/model-binding). Testy, które obejmują interakcje między składnikami, które zbiorczo odpowiadają na żądanie, są obsługiwane przez *testy integracji*. Aby uzyskać więcej informacji na temat testów integracji, zobacz <xref:test/integration-tests> .

Jeśli piszesz filtry niestandardowe i trasy, przetestuj je jednostkowo, a nie jako część testów dla konkretnej akcji kontrolera.

Aby zademonstrować testy jednostkowe kontrolera, przejrzyj następujący kontroler w przykładowej aplikacji. Kontroler Home wyświetla listę sesji burzy mózgów i umożliwia tworzenie nowych sesji burzy mózgów przy użyciu żądania POST:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Poprzedni kontroler:

* Zgodnie z [zasadą jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Oczekuje [iniekcji zależności (di)](xref:fundamentals/dependency-injection) , aby zapewnić wystąpienie `IBrainstormSessionRepository` .
* Program może być testowany z usługą ze makietą `IBrainstormSessionRepository` przy użyciu struktury obiektów imitacji, takiej jak [MOQ](https://www.nuget.org/packages/Moq/). *Obiekt imitacji* jest obiektem prefabrykowanym ze wstępnie określonym zestawem zachowań właściwości i metod używanych do testowania. Aby uzyskać więcej informacji, zobacz [wprowadzenie do testów integracji](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index`Metoda nie ma pętli ani rozgałęziania i wywołuje tylko jedną metodę. Test jednostkowy dla tej akcji:

* Program umożliwia imitację `IBrainstormSessionRepository` usługi przy użyciu `GetTestSessions` metody. `GetTestSessions`tworzy dwie sesje z burzą mózgów z datami i nazwami sesji.
* Wykonuje `Index` metodę.
* Wykonuje potwierdzenia w wyniku zwróconym przez metodę:
  * <xref:Microsoft.AspNetCore.Mvc.ViewResult>Jest zwracany.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) to `StormSessionViewModel` .
  * W programie są przechowywane dwie sesje burzy mózgów `ViewDataDictionary.Model` .

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Testy metody kontrolera macierzystego `HTTP POST Index` sprawdzają, czy:

* Gdy [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) jest `false` , metoda akcja zwraca *400 Nieprawidłowe żądanie* <xref:Microsoft.AspNetCore.Mvc.ViewResult> z odpowiednimi danymi.
* Gdy `ModelState.IsValid` jest `true` :
  * `Add`Metoda w repozytorium jest wywoływana.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>Jest zwracany z prawidłowymi argumentami.

Nieprawidłowy stan modelu jest testowany przez dodanie błędów przy użyciu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> , jak pokazano w pierwszym teście poniżej:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Gdy [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) jest nieprawidłowy, jest on `ViewResult` zwracany jako dla żądania GET. Test nie próbuje przekazać nieprawidłowego modelu. Przekazywanie nieprawidłowego modelu nie jest prawidłowym podejściem, ponieważ powiązanie modelu nie jest uruchomione (mimo że [test integracji](xref:test/integration-tests) używa powiązania modelu). W takim przypadku powiązanie modelu nie jest testowane. Te testy jednostkowe służą wyłącznie do testowania kodu w metodzie akcji.

Drugi test weryfikuje, czy gdy `ModelState` jest prawidłowy:

* Dodano nowy element `BrainstormSession` (za pośrednictwem repozytorium).
* Metoda zwraca obiekt `RedirectToActionResult` z oczekiwanymi właściwościami.

Wywołane wywołania, które nie są wywoływane, są zwykle ignorowane, ale wywołanie `Verifiable` na końcu wywołania Instalatora pozwala na weryfikację makiety w teście. Jest to wykonywane z wywołaniem `mockRepo.Verify` , które kończy się niepowodzeniem testu, jeśli oczekiwana Metoda nie została wywołana.

> [!NOTE]
> Biblioteka MOQ używana w tym przykładzie umożliwia mieszanie możliwej do zweryfikowania lub "ścisłych", imitacje z niemożliwymi do zweryfikowania makietami (nazywanymi również "luźnymi" fragmentami lub wycinkami). Dowiedz się więcej o [dostosowywaniu zachowań makiety za pomocą MOQ](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).

[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) w aplikacji przykładowej wyświetla informacje powiązane z określoną sesją burzy mózgów. Kontroler zawiera logikę do działania z nieprawidłowymi `id` wartościami (Istnieją dwa `return` scenariusze w poniższym przykładzie, aby pokryć te scenariusze). Instrukcja Final `return` zwraca nowy `StormSessionViewModel` Widok (*controllers/SessionController. cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Testy jednostkowe obejmują jeden test dla każdego `return` scenariusza w akcji kontrolera sesji `Index` :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Przejście do kontrolera pomysłów powoduje, że aplikacja ujawnia funkcje jako interfejs API sieci Web na `api/ideas` trasie:

* Lista pomysłów ( `IdeaDTO` ) skojarzonych z sesją burzy mózgów jest zwracana przez `ForSession` metodę.
* `Create`Metoda dodaje nowe pomysły do sesji.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Unikaj zwracania jednostek domeny biznesowej bezpośrednio za pośrednictwem wywołań interfejsu API. Jednostki domeny:

* Często zawierają więcej danych niż jest to wymagane przez klienta.
* Niepotrzebne połączenie wewnętrznego modelu domeny aplikacji z publicznie uwidocznionym interfejsem API.

Można wykonać mapowanie między jednostkami domeny a typami zwracanymi do klienta:

* Ręczne korzystanie z programu LINQ `Select` jako aplikacji przykładowej. Aby uzyskać więcej informacji, zobacz [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automatycznie z biblioteką, taką jak [automapowania](https://github.com/AutoMapper/AutoMapper).

Następnie w przykładowej aplikacji przedstawiono testy jednostkowe dla `Create` `ForSession` metod interfejsu API i kontrolera pomysłów.

Przykładowa aplikacja zawiera dwa `ForSession` testy. Pierwszy test określa, czy `ForSession` zwracany jest komunikat <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (nie znaleziono protokołu HTTP) dla nieprawidłowej sesji:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Drugi `ForSession` test określa, czy `ForSession` zwraca listę koncepcji sesji ( `<List<IdeaDTO>>` ) dla prawidłowej sesji. Sprawdza także, czy najpierw należy potwierdzić, że `Name` Właściwość jest poprawna:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Aby przetestować zachowanie `Create` metody `ModelState` , gdy jest nieprawidłowa, przykładowa aplikacja dodaje błąd modelu do kontrolera w ramach testu. Nie próbuj testować walidacji modelu lub powiązania modelu w testach jednostkowych po &mdash; prostu Przetestuj zachowanie metody akcji, gdy wystąpi nieprawidłowe `ModelState` :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Drugi test zależy od `Create` zwracanego repozytorium `null` , więc repozytorium makiety jest skonfigurowane do zwrócenia `null` . Nie ma potrzeby tworzenia testowej bazy danych (w pamięci lub w inny sposób) i konstruowania zapytania zwracającego ten wynik. Test można wykonać w pojedynczej instrukcji, jak przykładowy kod ilustruje:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Trzeci `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession` sprawdza, czy `UpdateAsync` Metoda repozytorium jest wywoływana. Makieta jest wywoływana z `Verifiable` , i wywoływana jest metoda z zastosowanego repozytorium `Verify` w celu potwierdzenia, że metoda możliwa do zweryfikowania jest wykonywana. To nie jest odpowiedzialność za testy jednostkowe, aby upewnić się, że `UpdateAsync` Metoda zapisała dane &mdash; , które można wykonać za pomocą testu integracji.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

W ASP.NET Core 2,1 lub nowszej [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ) umożliwia zwrócenie typu pochodnego `ActionResult` lub zwracanego określonego typu.

Przykładowa aplikacja zawiera metodę zwracającą `List<IdeaDTO>` dla danej sesji `id` . Jeśli sesja `id` nie istnieje, kontroler zwraca <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Dwa testy `ForSessionActionResult` kontrolera są zawarte w `ApiIdeasControllerTests` .

Pierwszy test potwierdza, że kontroler zwróci `ActionResult` nieistniejącą listę pomysłów dla nieistniejącej sesji `id` :

* `ActionResult`Typ to `ActionResult<List<IdeaDTO>>` .
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>Jest <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> .

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

W przypadku prawidłowej sesji `id` drugi test potwierdza, że metoda zwraca:

* `ActionResult`Z `List<IdeaDTO>` typem.
* [ActionResult \<T> . Wartość](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) jest `List<IdeaDTO>` typem.
* Pierwszy element na liście jest prawidłowym pomysłem pasującym do pomysłu przechowywanego w sesji makiety (uzyskany przez wywołanie `GetTestSession` ).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Przykładowa aplikacja zawiera również metodę tworzenia nowego `Idea` dla danej sesji. Kontroler zwraca:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>dla nieprawidłowego modelu.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Jeśli sesja nie istnieje.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>gdy sesja zostanie zaktualizowana przy użyciu nowego pomysłu.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Trzy testy `CreateActionResult` są zawarte w `ApiIdeasControllerTests` .

Pierwszy tekst potwierdza, że <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> jest zwracany dla nieprawidłowego modelu.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Drugi test sprawdza, czy <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> jest zwracany, jeśli sesja nie istnieje.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

W przypadku prawidłowej sesji `id` test końcowy potwierdza, że:

* Metoda zwraca obiekt `ActionResult` z `BrainstormSession` typem.
* [ActionResult \<T> . Wynik](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) to <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult`jest analogiczny do *201 utworzonej* odpowiedzi z `Location` nagłówkiem.
* [ActionResult \<T> . Wartość](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) jest `BrainstormSession` typem.
* Wywołano wywołanie makiety w celu zaktualizowania sesji `UpdateAsync(testSession)` . `Verifiable`Wywołanie metody jest sprawdzane przez wykonanie `mockRepo.Verify()` w potwierdzeniach.
* `Idea`Dla sesji zwracane są dwa obiekty.
* Ostatni element ( `Idea` dodany przez wywołanie makiety do `UpdateAsync` ) jest zgodny z `newIdea` dodaniem do sesji w teście.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/integration-tests>
* [Tworzenie i uruchamianie testów jednostkowych za pomocą programu Visual Studio](/visualstudio/test/unit-test-your-code)
* [AspNetCore. MVC-Fluent — Biblioteka testów dla ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Biblioteka testów jednostkowych z jednoznacznie określonymi typami, dostarczająca interfejs Fluent do testowania aplikacji MVC i Web API. (*Niekonserwowane lub obsługiwane przez firmę Microsoft).*

