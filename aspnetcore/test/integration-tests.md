---
<span data-ttu-id="80233-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-102">'Blazor'</span></span>
- <span data-ttu-id="80233-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-103">'Identity'</span></span>
- <span data-ttu-id="80233-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-105">'Razor'</span></span>
- <span data-ttu-id="80233-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="80233-107">Testy integracji w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80233-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="80233-108">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Kowalski](https://ardalis.com/)i [Jos van der](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="80233-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80233-109">Testy integracji zapewniają, że składniki aplikacji działają prawidłowo na poziomie, który obejmuje infrastrukturę pomocniczą aplikacji, taką jak baza danych, system plików i sieć.</span><span class="sxs-lookup"><span data-stu-id="80233-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="80233-110">ASP.NET Core obsługuje testy integracji przy użyciu struktury testów jednostkowych z testowym hostem sieci Web i serwerem testowym w pamięci.</span><span class="sxs-lookup"><span data-stu-id="80233-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="80233-111">W tym temacie założono podstawową wiedzę na temat testów jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="80233-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="80233-112">Jeśli nie znasz pojęć testowych, zobacz [testy jednostkowe w programie .NET Core i w .NET Standard](/dotnet/core/testing/) tematu oraz jego połączonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="80233-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80233-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="80233-114">Przykładowa aplikacja jest Razor aplikacją stron i przyjmuje podstawowe informacje o Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="80233-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="80233-115">Jeśli nie znasz Razor stron, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="80233-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="80233-116">[Wprowadzenie do Razor stron](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="80233-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="80233-117">[Wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="80233-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="80233-118">[RazorTesty jednostkowe stron](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="80233-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="80233-119">W przypadku testowania aplikacji jednostronicowych zaleca się używanie narzędzia, takiego jak [selen](https://www.seleniumhq.org/), które umożliwia automatyzację przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="80233-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="80233-120">Wprowadzenie do testów integracji</span><span class="sxs-lookup"><span data-stu-id="80233-120">Introduction to integration tests</span></span>

<span data-ttu-id="80233-121">Testy integracji ocenią składniki aplikacji na szerszym poziomie niż [testy jednostkowe](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="80233-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="80233-122">Testy jednostkowe służą do testowania izolowanych składników oprogramowania, takich jak poszczególne metody klasy.</span><span class="sxs-lookup"><span data-stu-id="80233-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="80233-123">Testy integracji potwierdzają, że co najmniej dwa składniki aplikacji współpracują ze sobą w celu uzyskania oczekiwanego wyniku, co może uwzględniać każdy składnik wymagany do pełnego przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="80233-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="80233-124">Te szersze testy są używane do testowania infrastruktury aplikacji i całego środowiska, często łącznie z następującymi składnikami:</span><span class="sxs-lookup"><span data-stu-id="80233-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="80233-125">baza danych</span><span class="sxs-lookup"><span data-stu-id="80233-125">Database</span></span>
* <span data-ttu-id="80233-126">System plików</span><span class="sxs-lookup"><span data-stu-id="80233-126">File system</span></span>
* <span data-ttu-id="80233-127">Urządzenia sieciowe</span><span class="sxs-lookup"><span data-stu-id="80233-127">Network appliances</span></span>
* <span data-ttu-id="80233-128">Potok żądania-odpowiedź</span><span class="sxs-lookup"><span data-stu-id="80233-128">Request-response pipeline</span></span>

<span data-ttu-id="80233-129">Testy jednostkowe wykorzystują składniki, *znane jako elementy* sztuczne lub *makiety*, zamiast składników infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="80233-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="80233-130">W przeciwieństwie do testów jednostkowych, testy integracji:</span><span class="sxs-lookup"><span data-stu-id="80233-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="80233-131">Użyj rzeczywistych składników używanych przez aplikację w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="80233-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="80233-132">Wymagaj większej ilości kodu i przetwarzania danych.</span><span class="sxs-lookup"><span data-stu-id="80233-132">Require more code and data processing.</span></span>
* <span data-ttu-id="80233-133">Trwa dłużej.</span><span class="sxs-lookup"><span data-stu-id="80233-133">Take longer to run.</span></span>

<span data-ttu-id="80233-134">W związku z tym Ogranicz korzystanie z testów integracji do najważniejszych scenariuszy infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="80233-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="80233-135">Jeśli można przetestować zachowanie przy użyciu testu jednostkowego lub testu integracji, wybierz test jednostkowy.</span><span class="sxs-lookup"><span data-stu-id="80233-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="80233-136">Nie zapisuj testów integracji dla każdej możliwej permutacji danych i dostępu do plików z bazami danych i systemami plików.</span><span class="sxs-lookup"><span data-stu-id="80233-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="80233-137">Bez względu na to, ile miejsc w aplikacji współdziała z bazami danych i systemami plików, skoncentrowany zestaw testów do odczytu, zapisu, aktualizacji i usuwania umożliwia zwykle testowanie składników bazy danych i systemu plików.</span><span class="sxs-lookup"><span data-stu-id="80233-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="80233-138">Użyj testów jednostkowych do rutynowych testów logiki metod, które współpracują z tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="80233-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="80233-139">W testach jednostkowych użycie sztucznych/imitacji infrastruktury powoduje szybsze wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="80233-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="80233-140">W dyskusjach dotyczących testów integracji testowany projekt jest często określany jako testowany *system*lub "SUT".</span><span class="sxs-lookup"><span data-stu-id="80233-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="80233-141">*"SUT" jest używany w tym temacie w celu odwoływania się do testowanej aplikacji ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="80233-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="80233-142">ASP.NET Core testy integracji</span><span class="sxs-lookup"><span data-stu-id="80233-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="80233-143">Testy integracji w ASP.NET Core wymagają następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="80233-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="80233-144">Projekt testowy służy do znajdowania i wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="80233-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="80233-145">Projekt testowy ma odwołanie do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="80233-146">Projekt testowy tworzy testowy host sieci Web dla SUT i używa klienta serwera testowego do obsługi żądań i odpowiedzi z SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="80233-147">Moduł uruchamiający testy służy do wykonywania testów i raportujących wyniki testów.</span><span class="sxs-lookup"><span data-stu-id="80233-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="80233-148">Testy integracji są zgodne z sekwencją zdarzeń, które obejmują typowe kroki testu *rozmieszczenia*, *działania*i *potwierdzeń* :</span><span class="sxs-lookup"><span data-stu-id="80233-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="80233-149">SUT hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="80233-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="80233-150">Klient serwera testowego jest tworzony w celu przesyłania żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="80233-151">Krok *rozmieszczania* testu jest wykonywany: aplikacja testowa przygotowuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="80233-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="80233-152">Krok testu *Act* jest wykonywany: klient przesyła żądanie i odbiera odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="80233-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="80233-153">Krok testu *potwierdzenia* jest wykonywany: *rzeczywista* odpowiedź jest sprawdzana jako *przebieg* lub *Niepowodzenie* w zależności od *oczekiwanej* odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="80233-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="80233-154">Proces jest kontynuowany, dopóki wszystkie testy nie zostaną wykonane.</span><span class="sxs-lookup"><span data-stu-id="80233-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="80233-155">Wyniki testu są zgłaszane.</span><span class="sxs-lookup"><span data-stu-id="80233-155">The test results are reported.</span></span>

<span data-ttu-id="80233-156">Test hosta sieci Web jest zwykle skonfigurowany inaczej niż normalny host sieci Web aplikacji dla przebiegów testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="80233-157">Na przykład dla testów można użyć innej bazy danych lub różnych ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="80233-158">Składniki infrastruktury, takie jak test hosta sieci Web i serwer testu w pamięci ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), są udostępniane lub zarządzane przez pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="80233-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="80233-159">Użycie tego pakietu usprawnia tworzenie i wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="80233-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="80233-160">`Microsoft.AspNetCore.Mvc.Testing`Pakiet obsługuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="80233-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="80233-161">Kopiuje plik zależności (*. deps*) z SUT do katalogu *bin* projektu testowego.</span><span class="sxs-lookup"><span data-stu-id="80233-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="80233-162">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) dla katalogu głównego projektu SUT, aby umożliwić znalezienie plików statycznych i stron/widoków podczas wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="80233-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="80233-163">Udostępnia klasę [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , aby usprawnić uruchamianie SUT przy użyciu `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="80233-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="80233-164">Dokumentacja [testów jednostkowych](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) opisuje sposób konfigurowania projektu testowego i modułu testowego, a także szczegółowe instrukcje dotyczące sposobu uruchamiania testów i zaleceń dotyczących sposobu określania nazw testów i klas testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="80233-165">Podczas tworzenia projektu testowego dla aplikacji należy oddzielić testy jednostkowe od testów integracji do różnych projektów.</span><span class="sxs-lookup"><span data-stu-id="80233-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="80233-166">Dzięki temu składniki do testowania infrastruktury nie są przypadkowo uwzględniane w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="80233-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="80233-167">Rozdzielenie testów jednostkowych i integracyjnych umożliwia również kontrolę nad tym, który zestaw testów jest uruchamiany.</span><span class="sxs-lookup"><span data-stu-id="80233-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="80233-168">Nie istnieje praktycznie żadna różnica między konfiguracją testów Razor aplikacji stron i aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="80233-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="80233-169">Jedyną różnicą jest to, jak te testy są nazywane.</span><span class="sxs-lookup"><span data-stu-id="80233-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="80233-170">W Razor aplikacji stronicowej testy punktów końcowych stron są zwykle nazywane po klasie modelu strony (na przykład `IndexPageTests` w celu przetestowania integracji składników na stronie indeksu).</span><span class="sxs-lookup"><span data-stu-id="80233-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="80233-171">W aplikacji MVC testy są zwykle zorganizowane według klas kontrolera i nazwane po testowaniu kontrolerów (na przykład `HomeControllerTests` w celu przetestowania integracji składników dla kontrolera macierzystego).</span><span class="sxs-lookup"><span data-stu-id="80233-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="80233-172">Wymagania wstępne aplikacji testowej</span><span class="sxs-lookup"><span data-stu-id="80233-172">Test app prerequisites</span></span>

<span data-ttu-id="80233-173">Projekt testowy musi:</span><span class="sxs-lookup"><span data-stu-id="80233-173">The test project must:</span></span>

* <span data-ttu-id="80233-174">Odwołuje się do pakietu [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="80233-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="80233-175">Określ zestaw SDK sieci Web w pliku projektu ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="80233-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="80233-176">Te wymagania wstępne można zobaczyć w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="80233-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="80233-177">Sprawdź plik *Tests/RazorPagesProject. Tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="80233-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="80233-178">Przykładowa aplikacja używa środowiska testowego [xUnit](https://xunit.github.io/) i biblioteki analizatora [AngleSharp](https://anglesharp.github.io/) , dzięki czemu Przykładowa aplikacja również odwołuje się do:</span><span class="sxs-lookup"><span data-stu-id="80233-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="80233-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="80233-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="80233-180">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="80233-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="80233-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="80233-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="80233-182">Entity Framework Core jest również używany w testach.</span><span class="sxs-lookup"><span data-stu-id="80233-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="80233-183">Odwołania do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80233-183">The app references:</span></span>

* [<span data-ttu-id="80233-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="80233-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="80233-185">[Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="80233-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="80233-186">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="80233-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="80233-187">Microsoft. EntityFrameworkCore. inMemory</span><span class="sxs-lookup"><span data-stu-id="80233-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="80233-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="80233-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="80233-189">Środowisko SUT</span><span class="sxs-lookup"><span data-stu-id="80233-189">SUT environment</span></span>

<span data-ttu-id="80233-190">Jeśli [środowisko](xref:fundamentals/environments) SUT nie jest ustawione, środowisko jest domyślnie stosowane do programowania.</span><span class="sxs-lookup"><span data-stu-id="80233-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="80233-191">Podstawowe testy z domyślną WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="80233-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="80233-192">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) służy do tworzenia [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) dla testów integracji.</span><span class="sxs-lookup"><span data-stu-id="80233-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="80233-193">`TEntryPoint`jest klasą punktu wejścia SUT, zazwyczaj `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="80233-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="80233-194">Klasy testowe implementują interfejs *armatury klasy* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) w celu wskazania, że Klasa zawiera testy i udostępnia wystąpienia obiektów udostępnionych w ramach testów w klasie.</span><span class="sxs-lookup"><span data-stu-id="80233-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="80233-195">Poniższa klasa testowa, `BasicTests` , używa `WebApplicationFactory` do ładowania początkowego SUT i zapewnienia [HttpClient](/dotnet/api/system.net.http.httpclient) do metody testowej `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="80233-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="80233-196">Metoda sprawdza, czy kod stanu odpowiedzi powiedzie się (kody stanu z zakresu 200-299), a `Content-Type` nagłówek jest `text/html; charset=utf-8` dla kilku stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="80233-197">[Klient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) tworzy wystąpienie `HttpClient` , które automatycznie następuje po przekierowaniu i obsłuży pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="80233-198">Domyślnie nieistotne pliki cookie nie są zachowywane między żądaniami, gdy [zasady zgody Rodo](xref:security/gdpr) są włączone.</span><span class="sxs-lookup"><span data-stu-id="80233-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="80233-199">Aby zachować nieistotne pliki cookie, takie jak te używane przez dostawcę TempData, oznacz je jako niezbędne w testach.</span><span class="sxs-lookup"><span data-stu-id="80233-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="80233-200">Aby uzyskać instrukcje dotyczące oznaczania pliku cookie jako kluczowego, zobacz [podstawowe pliki cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="80233-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="80233-201">Dostosuj WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="80233-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="80233-202">Konfigurację hosta sieci Web można utworzyć niezależnie od klas testów przez dziedziczenie z `WebApplicationFactory` programu w celu utworzenia jednego lub kilku fabryk niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="80233-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="80233-203">Dziedzicz z `WebApplicationFactory` i Zastąp [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="80233-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="80233-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umożliwia konfigurację kolekcji usług z [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="80233-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="80233-205">Wypełnianie bazy danych w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) jest wykonywane przez `InitializeDbForTests` metodę.</span><span class="sxs-lookup"><span data-stu-id="80233-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="80233-206">Metoda jest opisana w [przykładowej testów integracji: sekcja testowa aplikacja w organizacji](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="80233-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="80233-207">Kontekst bazy danych SUT jest zarejestrowany w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="80233-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="80233-208">`builder.ConfigureServices`Wywołanie zwrotne aplikacji testowej jest wykonywane *po* `Startup.ConfigureServices` wykonaniu kodu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="80233-209">Kolejność wykonywania jest istotną zmianą dla [hosta ogólnego](xref:fundamentals/host/generic-host) w wersji ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="80233-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="80233-210">Aby użyć innej bazy danych dla testów niż baza danych aplikacji, należy zastąpić kontekst bazy danych aplikacji `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="80233-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="80233-211">W przypadku SUTs, które nadal korzystają z [hosta sieci Web} (linki XREF: podstawy/Host/Web-Host), `builder.ConfigureServices` wywołanie zwrotne aplikacji testowej jest wykonywane *przed* `Startup.ConfigureServices` kodem SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-211">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="80233-212">`builder.ConfigureTestServices`Wywołanie zwrotne aplikacji testowej jest wykonywane *po*.</span><span class="sxs-lookup"><span data-stu-id="80233-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="80233-213">Przykładowa aplikacja znajduje deskryptor usługi dla kontekstu bazy danych i używa deskryptora do usunięcia rejestracji usługi.</span><span class="sxs-lookup"><span data-stu-id="80233-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="80233-214">Następnie fabryka dodaje nową `ApplicationDbContext` , która korzysta z bazy danych w pamięci dla testów.</span><span class="sxs-lookup"><span data-stu-id="80233-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="80233-215">Aby nawiązać połączenie z inną bazą danych niż baza danych w pamięci, Zmień `UseInMemoryDatabase` wywołanie w celu połączenia kontekstu z inną bazą danych.</span><span class="sxs-lookup"><span data-stu-id="80233-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="80233-216">Aby użyć SQL Server testowej bazy danych:</span><span class="sxs-lookup"><span data-stu-id="80233-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="80233-217">Odwołuje się do pakietu NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="80233-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="80233-218">Wywołaj `UseSqlServer` Parametry połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="80233-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="80233-219">Użyj niestandardowych `CustomWebApplicationFactory` klas testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="80233-220">W poniższym przykładzie zastosowano fabrykę w `IndexPageTests` klasie:</span><span class="sxs-lookup"><span data-stu-id="80233-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="80233-221">Klient aplikacji przykładowej jest skonfigurowany tak, aby uniemożliwiać `HttpClient` następujące przekierowania.</span><span class="sxs-lookup"><span data-stu-id="80233-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="80233-222">Jak wyjaśniono w dalszej części sekcji [uwierzytelnianie](#mock-authentication) , pozwala to testom sprawdzić wynik pierwszej odpowiedzi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="80233-223">Pierwsza odpowiedź to przekierowanie w wielu z tych testów z `Location` nagłówkiem.</span><span class="sxs-lookup"><span data-stu-id="80233-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="80233-224">Typowy test używa `HttpClient` metod i, aby przetworzyć żądanie i odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="80233-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="80233-225">Wszelkie żądania POST do SUT muszą być zgodne z sprawdzeniem, czy jest ono automatycznie wykonywane przez [system ochrony danych przed fałszerstwem](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="80233-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="80233-226">Aby można było rozmieścić żądanie POST testu, aplikacja testowa musi:</span><span class="sxs-lookup"><span data-stu-id="80233-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="80233-227">Utwórz żądanie dla strony.</span><span class="sxs-lookup"><span data-stu-id="80233-227">Make a request for the page.</span></span>
1. <span data-ttu-id="80233-228">Przeanalizuj plik cookie dotyczący fałszowania i token walidacji żądania z odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="80233-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="80233-229">Wprowadź żądanie POST przy użyciu pliku cookie służącego do fałszerstwa i tokenu walidacji żądania.</span><span class="sxs-lookup"><span data-stu-id="80233-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="80233-230">`SendAsync`Metody rozszerzenia pomocnika (*pomocnicys/HttpClientExtensions. cs*) i `GetDocumentAsync` metoda pomocnika (*pomocnicys/HtmlHelpers. cs*) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) używają analizatora [AngleSharp](https://anglesharp.github.io/) do obsługi kontroli przed fałszerstwem przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="80233-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="80233-231">`GetDocumentAsync`&ndash;Odbiera [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) i zwraca `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="80233-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="80233-232">`GetDocumentAsync`używa fabryki przygotowującej *odpowiedź wirtualną* na podstawie oryginału `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="80233-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="80233-233">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="80233-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="80233-234">`SendAsync`metody rozszerzające `HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) i Call [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) do przesyłania żądań do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="80233-235">Przeciążenia dla `SendAsync` Zaakceptuj formularz HTML ( `IHtmlFormElement` ) i następujące:</span><span class="sxs-lookup"><span data-stu-id="80233-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="80233-236">Przycisk przesyłania formularza ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="80233-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="80233-237">Kolekcja wartości formularza ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="80233-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="80233-238">Przycisk Prześlij ( `IHtmlElement` ) i wartości formularza ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="80233-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="80233-239">[AngleSharp](https://anglesharp.github.io/) to biblioteka analizy innej firmy używana do celów demonstracyjnych w tym temacie i Przykładowa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="80233-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="80233-240">AngleSharp nie jest obsługiwana ani wymagana do testowania integracji aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80233-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="80233-241">Można użyć innych analizatorów, takich jak [pakiet HAP (html)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="80233-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="80233-242">Innym podejściem jest napisać kod, który będzie obsługiwał token weryfikacji żądań systemu i bezfałszowający plik cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="80233-243">Dostosowywanie klienta przy użyciu WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="80233-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="80233-244">Gdy w metodzie testowej jest wymagana dodatkowa konfiguracja, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) tworzy nową `WebApplicationFactory` z [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , który jest bardziej dostosowany przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="80233-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="80233-245">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`Metoda testowa [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) pokazuje użycie `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80233-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="80233-246">Ten test służy do usuwania rekordów w bazie danych przez wyzwolenie przesłania formularza w SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="80233-247">Ponieważ inny test w `IndexPageTests` klasie wykonuje operację, która usuwa wszystkie rekordy z bazy danych i może być uruchomiona przed `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodą, baza danych zostanie oddana w tej metodzie testowej, aby upewnić się, że rekord jest obecny dla SUT do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80233-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="80233-248">Wybranie pierwszego przycisku Usuń `messages` formularza w SUT jest symulowane w żądaniu do SUT:</span><span class="sxs-lookup"><span data-stu-id="80233-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="80233-249">Opcje klienta</span><span class="sxs-lookup"><span data-stu-id="80233-249">Client options</span></span>

<span data-ttu-id="80233-250">W poniższej tabeli przedstawiono domyślne [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostępne podczas tworzenia `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="80233-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="80233-251">Opcja</span><span class="sxs-lookup"><span data-stu-id="80233-251">Option</span></span> | <span data-ttu-id="80233-252">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-252">Description</span></span> | <span data-ttu-id="80233-253">Domyślne</span><span class="sxs-lookup"><span data-stu-id="80233-253">Default</span></span> |
| ---
<span data-ttu-id="80233-254">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-255">'Blazor'</span></span>
- <span data-ttu-id="80233-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-256">'Identity'</span></span>
- <span data-ttu-id="80233-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-258">'Razor'</span></span>
- <span data-ttu-id="80233-259">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-259">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-260">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-261">'Blazor'</span></span>
- <span data-ttu-id="80233-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-262">'Identity'</span></span>
- <span data-ttu-id="80233-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-264">'Razor'</span></span>
- <span data-ttu-id="80233-265">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-266">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-267">'Blazor'</span></span>
- <span data-ttu-id="80233-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-268">'Identity'</span></span>
- <span data-ttu-id="80233-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-270">'Razor'</span></span>
- <span data-ttu-id="80233-271">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-272">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-273">'Blazor'</span></span>
- <span data-ttu-id="80233-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-274">'Identity'</span></span>
- <span data-ttu-id="80233-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-276">'Razor'</span></span>
- <span data-ttu-id="80233-277">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-277">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-278">------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-279">'Blazor'</span></span>
- <span data-ttu-id="80233-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-280">'Identity'</span></span>
- <span data-ttu-id="80233-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-282">'Razor'</span></span>
- <span data-ttu-id="80233-283">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-283">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Pobiera lub ustawia, czy `HttpClient` wystąpienia powinny automatycznie śledzić odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="80233-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="80233-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Pobiera lub ustawia podstawowy adres `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="80233-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="80233-286"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Pobiera lub ustawia, czy `HttpClient` wystąpienia powinny obsługiwać pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="80233-287"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Pobiera lub ustawia maksymalną liczbę odpowiedzi przekierowań, które `HttpClient` powinny być zgodne z wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="80233-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="80233-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="80233-288">| 7 |</span></span>

<span data-ttu-id="80233-289">Utwórz `WebApplicationFactoryClientOptions` klasę i przekaż ją do metody [onclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (wartości domyślne są pokazane w przykładzie kodu):</span><span class="sxs-lookup"><span data-stu-id="80233-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="80233-290">Wsuń usługi imitacji</span><span class="sxs-lookup"><span data-stu-id="80233-290">Inject mock services</span></span>

<span data-ttu-id="80233-291">Usługi można przesłaniać w teście, używając wywołania [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) na konstruktorze hosta.</span><span class="sxs-lookup"><span data-stu-id="80233-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="80233-292">**Aby wstrzyknąć usługi makiety, SUT musi mieć `Startup` klasę z `Startup.ConfigureServices` metodą.**</span><span class="sxs-lookup"><span data-stu-id="80233-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="80233-293">Przykład SUT obejmuje usługę objętą zakresem zwracającą ofertę.</span><span class="sxs-lookup"><span data-stu-id="80233-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="80233-294">Po zażądaniu strony indeksu oferta zostanie osadzona w ukrytym polu na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="80233-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="80233-295">*Usługi/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="80233-296">*Usługi/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="80233-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="80233-298">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="80233-299">*Strony/indeks. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="80233-300">Po uruchomieniu aplikacji SUT jest generowany następujący znacznik:</span><span class="sxs-lookup"><span data-stu-id="80233-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="80233-301">W celu przetestowania usługi i iniekcji cytatu w teście integracji, usługa makiety jest wstrzykiwana do SUT przez test.</span><span class="sxs-lookup"><span data-stu-id="80233-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="80233-302">Usługa makiety zastępuje aplikację `QuoteService` za pomocą usługi dostarczonej przez aplikację testową o nazwie `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="80233-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="80233-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="80233-304">`ConfigureTestServices`jest wywoływana, a usługa o określonym zakresie jest zarejestrowana:</span><span class="sxs-lookup"><span data-stu-id="80233-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="80233-305">Adiustacje powstające podczas wykonywania testu odzwierciedlają tekst cytatu dostarczony przez `TestQuoteService` , w związku z czym potwierdzenie przebiega:</span><span class="sxs-lookup"><span data-stu-id="80233-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="80233-306">Uwierzytelnianie w postaci makiety</span><span class="sxs-lookup"><span data-stu-id="80233-306">Mock authentication</span></span>

<span data-ttu-id="80233-307">Testy w `AuthTests` klasie sprawdzają, czy bezpieczny punkt końcowy:</span><span class="sxs-lookup"><span data-stu-id="80233-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="80233-308">Przekierowuje nieuwierzytelnionego użytkownika do strony logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="80233-309">Zwraca zawartość dla uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80233-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="80233-310">W SUT `/SecurePage` Strona używa konwencji [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) w celu zastosowania [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) do strony.</span><span class="sxs-lookup"><span data-stu-id="80233-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="80233-311">Aby uzyskać więcej informacji, zobacz [ Razor strony konwencje autoryzacji](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="80233-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="80233-312">W `Get_SecurePageRedirectsAnUnauthenticatedUser` teście [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) jest ustawiony tak, aby nie zezwalać na przekierowania przez ustawienie [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na `false` :</span><span class="sxs-lookup"><span data-stu-id="80233-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="80233-313">W przypadku niezezwalania klientowi na śledzenie przekierowania można wykonać następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="80233-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="80233-314">Kod stanu zwracany przez SUT można sprawdzić pod kątem oczekiwanego wyniku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , a nie do końcowego kodu stanu po przekierowaniu do strony logowania, która byłaby [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="80233-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="80233-315">`Location`Wartość nagłówka w nagłówkach odpowiedzi jest sprawdzana w celu potwierdzenia, że zaczyna się od `http://localhost/Identity/Account/Login` , a nie od końcowej odpowiedzi na stronę logowania, gdzie `Location` nagłówek nie może być obecny.</span><span class="sxs-lookup"><span data-stu-id="80233-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="80233-316">Aplikacja testowa może zasymulować <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w programie w celu przetestowania aspektów uwierzytelniania i autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="80233-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="80233-317">Minimalny scenariusz zwraca element [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="80233-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="80233-318">`TestAuthHandler`Jest wywoływana w celu uwierzytelnienia użytkownika, gdy w schemacie uwierzytelniania jest ustawiona wartość `Test` Where `AddAuthentication` jest zarejestrowana dla `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="80233-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="80233-319">Aby uzyskać więcej informacji na temat `WebApplicationFactoryClientOptions` , zobacz sekcję [Opcje klienta](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="80233-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="80233-320">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="80233-320">Set the environment</span></span>

<span data-ttu-id="80233-321">Domyślnie środowisko hosta i aplikacji SUT jest skonfigurowane do korzystania ze środowiska deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="80233-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="80233-322">Aby zastąpić środowisko SUT przy użyciu `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80233-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="80233-323">Ustaw `ASPNETCORE_ENVIRONMENT` zmienną środowiskową (na przykład, `Staging` `Production` lub inną wartość niestandardową, taką jak `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="80233-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="80233-324">Przesłoń `CreateHostBuilder` aplikację testową, aby odczytać zmienne środowiskowe poprzedzone prefiksem `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="80233-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="80233-325">Jeśli SUT używa hosta sieci Web ( `IWebHostBuilder` ), Przesłoń `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="80233-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="80233-326">Jak infrastruktura testowa wnioskuje ścieżkę katalogu głównego zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="80233-327">`WebApplicationFactory`Konstruktor wnioskuje ścieżkę [katalogu głównego zawartości](xref:fundamentals/index#content-root) aplikacji, wyszukując [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na zestawie zawierającym testy integracji z kluczem równym `TEntryPoint` zestawowi `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="80233-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="80233-328">W przypadku nieznalezienia atrybutu o poprawnym kluczu `WebApplicationFactory` Wróć do wyszukiwania pliku rozwiązania (*. sln*) i dołączaj `TEntryPoint` nazwę zestawu do katalogu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="80233-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="80233-329">Katalog główny aplikacji (ścieżka katalogu głównego zawartości) służy do odnajdywania widoków i plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="80233-330">Wyłącz kopiowanie w tle</span><span class="sxs-lookup"><span data-stu-id="80233-330">Disable shadow copying</span></span>

<span data-ttu-id="80233-331">Kopiowanie w tle powoduje, że testy są wykonywane w innym katalogu niż katalog wyjściowy.</span><span class="sxs-lookup"><span data-stu-id="80233-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="80233-332">Aby testy działały prawidłowo, kopiowanie w tle musi być wyłączone.</span><span class="sxs-lookup"><span data-stu-id="80233-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="80233-333">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) używa xUnit i wyłącza kopiowanie w tle dla xUnit, dołączając plik *xUnit. Runner. JSON* z prawidłowym ustawieniem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="80233-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="80233-334">Aby uzyskać więcej informacji, zobacz [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="80233-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="80233-335">Dodaj plik *xUnit. Runner. JSON* do katalogu głównego projektu testowego z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="80233-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="80233-336">Usuwanie obiektów</span><span class="sxs-lookup"><span data-stu-id="80233-336">Disposal of objects</span></span>

<span data-ttu-id="80233-337">Po `IClassFixture` wykonaniu testów wdrożenia [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) i [HttpClient](/dotnet/api/system.net.http.httpclient) są usuwane, gdy xUnit usuwa [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="80233-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="80233-338">Jeśli obiekty utworzone przez dewelopera wymagają usunięcia, usuń je w `IClassFixture` implementacji.</span><span class="sxs-lookup"><span data-stu-id="80233-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="80233-339">Aby uzyskać więcej informacji, zobacz [implementowanie metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="80233-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="80233-340">Przykład testów integracji</span><span class="sxs-lookup"><span data-stu-id="80233-340">Integration tests sample</span></span>

<span data-ttu-id="80233-341">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) składa się z dwóch aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80233-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="80233-342">Aplikacja</span><span class="sxs-lookup"><span data-stu-id="80233-342">App</span></span> | <span data-ttu-id="80233-343">Katalog projektu</span><span class="sxs-lookup"><span data-stu-id="80233-343">Project directory</span></span> | <span data-ttu-id="80233-344">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-344">Description</span></span> |
| --- | ---
<span data-ttu-id="80233-345">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-346">'Blazor'</span></span>
- <span data-ttu-id="80233-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-347">'Identity'</span></span>
- <span data-ttu-id="80233-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-349">'Razor'</span></span>
- <span data-ttu-id="80233-350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-351">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-352">'Blazor'</span></span>
- <span data-ttu-id="80233-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-353">'Identity'</span></span>
- <span data-ttu-id="80233-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-355">'Razor'</span></span>
- <span data-ttu-id="80233-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-357">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-358">'Blazor'</span></span>
- <span data-ttu-id="80233-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-359">'Identity'</span></span>
- <span data-ttu-id="80233-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-361">'Razor'</span></span>
- <span data-ttu-id="80233-362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-363">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-364">'Blazor'</span></span>
- <span data-ttu-id="80233-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-365">'Identity'</span></span>
- <span data-ttu-id="80233-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-367">'Razor'</span></span>
- <span data-ttu-id="80233-368">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-369">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-370">'Blazor'</span></span>
- <span data-ttu-id="80233-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-371">'Identity'</span></span>
- <span data-ttu-id="80233-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-373">'Razor'</span></span>
- <span data-ttu-id="80233-374">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-375">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-376">'Blazor'</span></span>
- <span data-ttu-id="80233-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-377">'Identity'</span></span>
- <span data-ttu-id="80233-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-379">'Razor'</span></span>
- <span data-ttu-id="80233-380">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-380">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-381">--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-382">'Blazor'</span></span>
- <span data-ttu-id="80233-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-383">'Identity'</span></span>
- <span data-ttu-id="80233-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-385">'Razor'</span></span>
- <span data-ttu-id="80233-386">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-387">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-388">'Blazor'</span></span>
- <span data-ttu-id="80233-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-389">'Identity'</span></span>
- <span data-ttu-id="80233-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-391">'Razor'</span></span>
- <span data-ttu-id="80233-392">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-393">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-394">'Blazor'</span></span>
- <span data-ttu-id="80233-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-395">'Identity'</span></span>
- <span data-ttu-id="80233-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-397">'Razor'</span></span>
- <span data-ttu-id="80233-398">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-398">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-399">------ | | Aplikacja wiadomości (SUT) | *src/RazorPagesProject* | Zezwala użytkownikowi na dodawanie, usuwanie, usuwanie wszystkich i analizowanie komunikatów.</span><span class="sxs-lookup"><span data-stu-id="80233-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="80233-400">| | Aplikacja testowa | *testy/RazorPagesProject. Tests* | Służy do integracji testu SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="80233-401">Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="80233-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="80233-402">W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w katalogu *Tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="80233-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="80233-403">Organizacja aplikacji wiadomości (SUT)</span><span class="sxs-lookup"><span data-stu-id="80233-403">Message app (SUT) organization</span></span>

<span data-ttu-id="80233-404">SUT to Razor system komunikatów stron o następujących cechach:</span><span class="sxs-lookup"><span data-stu-id="80233-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="80233-405">Strona indeks aplikacji (*Pages/index. cshtml* i *Pages/index. cshtml. cs*) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (średnia liczba wyrazów na komunikat).</span><span class="sxs-lookup"><span data-stu-id="80233-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="80233-406">Komunikat jest opisywany przez `Message` klasę (*Data/Message. cs*) z dwiema właściwościami: `Id` (Key) i `Text` (Message).</span><span class="sxs-lookup"><span data-stu-id="80233-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="80233-407">`Text`Właściwość jest wymagana i jest ograniczona do 200 znaków.</span><span class="sxs-lookup"><span data-stu-id="80233-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="80233-408">Komunikaty są przechowywane przy użyciu&#8224; [bazy danych Entity Framework w pamięci](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="80233-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="80233-409">Aplikacja zawiera warstwę dostępu do danych (DAL) w swojej klasie kontekstu bazy danych `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="80233-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="80233-410">Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów.</span><span class="sxs-lookup"><span data-stu-id="80233-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="80233-411">Aplikacja zawiera dostęp do programu `/SecurePage` , do którego jest dostępny tylko uwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="80233-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="80233-412">&#8224;temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory)— wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest.</span><span class="sxs-lookup"><span data-stu-id="80233-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="80233-413">W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="80233-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="80233-414">Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.</span><span class="sxs-lookup"><span data-stu-id="80233-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="80233-415">Mimo że aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (pracownicy)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor strony programu obsługują te wzorce rozwoju.</span><span class="sxs-lookup"><span data-stu-id="80233-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="80233-416">Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i [logiki kontrolera testów](/aspnet/core/mvc/controllers/testing) (przykład implementuje wzorzec repozytorium).</span><span class="sxs-lookup"><span data-stu-id="80233-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="80233-417">Testuj organizację aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-417">Test app organization</span></span>

<span data-ttu-id="80233-418">Aplikacja testowa to Aplikacja konsolowa w katalogu *Tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="80233-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="80233-419">Testuj katalog aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-419">Test app directory</span></span> | <span data-ttu-id="80233-420">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-420">Description</span></span> |
| ---
<span data-ttu-id="80233-421">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-422">'Blazor'</span></span>
- <span data-ttu-id="80233-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-423">'Identity'</span></span>
- <span data-ttu-id="80233-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-425">'Razor'</span></span>
- <span data-ttu-id="80233-426">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-427">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-428">'Blazor'</span></span>
- <span data-ttu-id="80233-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-429">'Identity'</span></span>
- <span data-ttu-id="80233-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-431">'Razor'</span></span>
- <span data-ttu-id="80233-432">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-433">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-434">'Blazor'</span></span>
- <span data-ttu-id="80233-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-435">'Identity'</span></span>
- <span data-ttu-id="80233-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-437">'Razor'</span></span>
- <span data-ttu-id="80233-438">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-439">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-440">'Blazor'</span></span>
- <span data-ttu-id="80233-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-441">'Identity'</span></span>
- <span data-ttu-id="80233-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-443">'Razor'</span></span>
- <span data-ttu-id="80233-444">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-445">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-446">'Blazor'</span></span>
- <span data-ttu-id="80233-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-447">'Identity'</span></span>
- <span data-ttu-id="80233-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-449">'Razor'</span></span>
- <span data-ttu-id="80233-450">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-451">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-452">'Blazor'</span></span>
- <span data-ttu-id="80233-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-453">'Identity'</span></span>
- <span data-ttu-id="80233-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-455">'Razor'</span></span>
- <span data-ttu-id="80233-456">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-457">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-458">'Blazor'</span></span>
- <span data-ttu-id="80233-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-459">'Identity'</span></span>
- <span data-ttu-id="80233-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-461">'Razor'</span></span>
- <span data-ttu-id="80233-462">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-462">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-463">--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-464">'Blazor'</span></span>
- <span data-ttu-id="80233-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-465">'Identity'</span></span>
- <span data-ttu-id="80233-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-467">'Razor'</span></span>
- <span data-ttu-id="80233-468">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-469">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-470">'Blazor'</span></span>
- <span data-ttu-id="80233-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-471">'Identity'</span></span>
- <span data-ttu-id="80233-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-473">'Razor'</span></span>
- <span data-ttu-id="80233-474">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-475">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-476">'Blazor'</span></span>
- <span data-ttu-id="80233-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-477">'Identity'</span></span>
- <span data-ttu-id="80233-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-479">'Razor'</span></span>
- <span data-ttu-id="80233-480">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-480">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-481">------ | | *AuthTests* | Zawiera metody testowe dla:</span><span class="sxs-lookup"><span data-stu-id="80233-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="80233-482">Uzyskiwanie dostępu do bezpiecznej strony przez nieuwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="80233-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="80233-483">Uzyskiwanie dostępu do bezpiecznej strony przez uwierzytelnionego użytkownika przy użyciu makiety <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="80233-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="80233-484">Uzyskiwanie profilu użytkownika usługi GitHub i sprawdzanie logowania użytkownika w profilu.</span><span class="sxs-lookup"><span data-stu-id="80233-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="80233-485">| | *BasicTests* | Zawiera metodę testową dla routingu i typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="80233-486">| | *IntegrationTests* | Zawiera testy integracji dla strony indeksu przy użyciu klasy niestandardowej `WebApplicationFactory` .</span><span class="sxs-lookup"><span data-stu-id="80233-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="80233-487">| | *Pomocnicy/narzędzia* | </span><span class="sxs-lookup"><span data-stu-id="80233-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="80233-488">*Utilities.cs* zawiera `InitializeDbForTests` metodę używaną do wypełniania bazy danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="80233-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="80233-489">*HtmlHelpers.cs* zapewnia metodę, która zwraca AngleSharp `IHtmlDocument` do użycia przez metody testowe.</span><span class="sxs-lookup"><span data-stu-id="80233-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="80233-490">*HttpClientExtensions.cs* zapewniają przeciążenia dla programu, `SendAsync` Aby przesyłać żądania do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="80233-491">Platforma testowa jest [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="80233-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="80233-492">Testy integracji są przeprowadzane przy użyciu [programu Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), który obejmuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="80233-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="80233-493">Ponieważ pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) jest używany do konfigurowania hosta testowego i serwera testowego, `TestHost` pakiety i `TestServer` nie wymagają bezpośrednich odwołań do pakietów w pliku projektu aplikacji testowej ani w konfiguracji dewelopera w aplikacji testowej.</span><span class="sxs-lookup"><span data-stu-id="80233-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="80233-494">**Umieszczanie bazy danych do testowania**</span><span class="sxs-lookup"><span data-stu-id="80233-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="80233-495">Testy integracji zwykle wymagają małego zestawu danych w bazie danych przed wykonaniem testu.</span><span class="sxs-lookup"><span data-stu-id="80233-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="80233-496">Na przykład można usunąć wywołania testu do usuwania rekordów bazy danych, więc baza danych musi mieć co najmniej jeden rekord, aby żądanie usunięcia zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="80233-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="80233-497">Przykładowa aplikacja odziarnauje bazę danych z trzema komunikatami w *Utilities.cs* , że testy mogą być używane podczas wykonywania:</span><span class="sxs-lookup"><span data-stu-id="80233-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="80233-498">Kontekst bazy danych SUT jest zarejestrowany w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="80233-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="80233-499">`builder.ConfigureServices`Wywołanie zwrotne aplikacji testowej jest wykonywane *po* `Startup.ConfigureServices` wykonaniu kodu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="80233-500">Aby użyć innej bazy danych dla testów, kontekst bazy danych aplikacji musi zostać zastąpiony `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="80233-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="80233-501">Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie WebApplicationFactory](#customize-webapplicationfactory) .</span><span class="sxs-lookup"><span data-stu-id="80233-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="80233-502">W przypadku SUTs, które nadal korzystają z [hosta sieci Web} (linki XREF: podstawy/Host/Web-Host), `builder.ConfigureServices` wywołanie zwrotne aplikacji testowej jest wykonywane *przed* `Startup.ConfigureServices` kodem SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-502">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="80233-503">`builder.ConfigureTestServices`Wywołanie zwrotne aplikacji testowej jest wykonywane *po*.</span><span class="sxs-lookup"><span data-stu-id="80233-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="80233-504">Testy integracji zapewniają, że składniki aplikacji działają prawidłowo na poziomie, który obejmuje infrastrukturę pomocniczą aplikacji, taką jak baza danych, system plików i sieć.</span><span class="sxs-lookup"><span data-stu-id="80233-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="80233-505">ASP.NET Core obsługuje testy integracji przy użyciu struktury testów jednostkowych z testowym hostem sieci Web i serwerem testowym w pamięci.</span><span class="sxs-lookup"><span data-stu-id="80233-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="80233-506">W tym temacie założono podstawową wiedzę na temat testów jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="80233-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="80233-507">Jeśli nie znasz pojęć testowych, zobacz [testy jednostkowe w programie .NET Core i w .NET Standard](/dotnet/core/testing/) tematu oraz jego połączonej zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="80233-508">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80233-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="80233-509">Przykładowa aplikacja jest Razor aplikacją stron i przyjmuje podstawowe informacje o Razor stronach.</span><span class="sxs-lookup"><span data-stu-id="80233-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="80233-510">Jeśli nie znasz Razor stron, zapoznaj się z następującymi tematami:</span><span class="sxs-lookup"><span data-stu-id="80233-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="80233-511">[Wprowadzenie do Razor stron](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="80233-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="80233-512">[Wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="80233-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="80233-513">[RazorTesty jednostkowe stron](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="80233-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="80233-514">W przypadku testowania aplikacji jednostronicowych zaleca się używanie narzędzia, takiego jak [selen](https://www.seleniumhq.org/), które umożliwia automatyzację przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="80233-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="80233-515">Wprowadzenie do testów integracji</span><span class="sxs-lookup"><span data-stu-id="80233-515">Introduction to integration tests</span></span>

<span data-ttu-id="80233-516">Testy integracji ocenią składniki aplikacji na szerszym poziomie niż [testy jednostkowe](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="80233-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="80233-517">Testy jednostkowe służą do testowania izolowanych składników oprogramowania, takich jak poszczególne metody klasy.</span><span class="sxs-lookup"><span data-stu-id="80233-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="80233-518">Testy integracji potwierdzają, że co najmniej dwa składniki aplikacji współpracują ze sobą w celu uzyskania oczekiwanego wyniku, co może uwzględniać każdy składnik wymagany do pełnego przetworzenia żądania.</span><span class="sxs-lookup"><span data-stu-id="80233-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="80233-519">Te szersze testy są używane do testowania infrastruktury aplikacji i całego środowiska, często łącznie z następującymi składnikami:</span><span class="sxs-lookup"><span data-stu-id="80233-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="80233-520">baza danych</span><span class="sxs-lookup"><span data-stu-id="80233-520">Database</span></span>
* <span data-ttu-id="80233-521">System plików</span><span class="sxs-lookup"><span data-stu-id="80233-521">File system</span></span>
* <span data-ttu-id="80233-522">Urządzenia sieciowe</span><span class="sxs-lookup"><span data-stu-id="80233-522">Network appliances</span></span>
* <span data-ttu-id="80233-523">Potok żądania-odpowiedź</span><span class="sxs-lookup"><span data-stu-id="80233-523">Request-response pipeline</span></span>

<span data-ttu-id="80233-524">Testy jednostkowe wykorzystują składniki, *znane jako elementy* sztuczne lub *makiety*, zamiast składników infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="80233-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="80233-525">W przeciwieństwie do testów jednostkowych, testy integracji:</span><span class="sxs-lookup"><span data-stu-id="80233-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="80233-526">Użyj rzeczywistych składników używanych przez aplikację w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="80233-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="80233-527">Wymagaj większej ilości kodu i przetwarzania danych.</span><span class="sxs-lookup"><span data-stu-id="80233-527">Require more code and data processing.</span></span>
* <span data-ttu-id="80233-528">Trwa dłużej.</span><span class="sxs-lookup"><span data-stu-id="80233-528">Take longer to run.</span></span>

<span data-ttu-id="80233-529">W związku z tym Ogranicz korzystanie z testów integracji do najważniejszych scenariuszy infrastruktury.</span><span class="sxs-lookup"><span data-stu-id="80233-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="80233-530">Jeśli można przetestować zachowanie przy użyciu testu jednostkowego lub testu integracji, wybierz test jednostkowy.</span><span class="sxs-lookup"><span data-stu-id="80233-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="80233-531">Nie zapisuj testów integracji dla każdej możliwej permutacji danych i dostępu do plików z bazami danych i systemami plików.</span><span class="sxs-lookup"><span data-stu-id="80233-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="80233-532">Bez względu na to, ile miejsc w aplikacji współdziała z bazami danych i systemami plików, skoncentrowany zestaw testów do odczytu, zapisu, aktualizacji i usuwania umożliwia zwykle testowanie składników bazy danych i systemu plików.</span><span class="sxs-lookup"><span data-stu-id="80233-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="80233-533">Użyj testów jednostkowych do rutynowych testów logiki metod, które współpracują z tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="80233-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="80233-534">W testach jednostkowych użycie sztucznych/imitacji infrastruktury powoduje szybsze wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="80233-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="80233-535">W dyskusjach dotyczących testów integracji testowany projekt jest często określany jako testowany *system*lub "SUT".</span><span class="sxs-lookup"><span data-stu-id="80233-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="80233-536">*"SUT" jest używany w tym temacie w celu odwoływania się do testowanej aplikacji ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="80233-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="80233-537">ASP.NET Core testy integracji</span><span class="sxs-lookup"><span data-stu-id="80233-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="80233-538">Testy integracji w ASP.NET Core wymagają następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="80233-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="80233-539">Projekt testowy służy do znajdowania i wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="80233-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="80233-540">Projekt testowy ma odwołanie do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="80233-541">Projekt testowy tworzy testowy host sieci Web dla SUT i używa klienta serwera testowego do obsługi żądań i odpowiedzi z SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="80233-542">Moduł uruchamiający testy służy do wykonywania testów i raportujących wyniki testów.</span><span class="sxs-lookup"><span data-stu-id="80233-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="80233-543">Testy integracji są zgodne z sekwencją zdarzeń, które obejmują typowe kroki testu *rozmieszczenia*, *działania*i *potwierdzeń* :</span><span class="sxs-lookup"><span data-stu-id="80233-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="80233-544">SUT hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="80233-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="80233-545">Klient serwera testowego jest tworzony w celu przesyłania żądań do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="80233-546">Krok *rozmieszczania* testu jest wykonywany: aplikacja testowa przygotowuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="80233-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="80233-547">Krok testu *Act* jest wykonywany: klient przesyła żądanie i odbiera odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="80233-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="80233-548">Krok testu *potwierdzenia* jest wykonywany: *rzeczywista* odpowiedź jest sprawdzana jako *przebieg* lub *Niepowodzenie* w zależności od *oczekiwanej* odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="80233-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="80233-549">Proces jest kontynuowany, dopóki wszystkie testy nie zostaną wykonane.</span><span class="sxs-lookup"><span data-stu-id="80233-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="80233-550">Wyniki testu są zgłaszane.</span><span class="sxs-lookup"><span data-stu-id="80233-550">The test results are reported.</span></span>

<span data-ttu-id="80233-551">Test hosta sieci Web jest zwykle skonfigurowany inaczej niż normalny host sieci Web aplikacji dla przebiegów testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="80233-552">Na przykład dla testów można użyć innej bazy danych lub różnych ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="80233-553">Składniki infrastruktury, takie jak test hosta sieci Web i serwer testu w pamięci ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), są udostępniane lub zarządzane przez pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .</span><span class="sxs-lookup"><span data-stu-id="80233-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="80233-554">Użycie tego pakietu usprawnia tworzenie i wykonywanie testów.</span><span class="sxs-lookup"><span data-stu-id="80233-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="80233-555">`Microsoft.AspNetCore.Mvc.Testing`Pakiet obsługuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="80233-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="80233-556">Kopiuje plik zależności (*. deps*) z SUT do katalogu *bin* projektu testowego.</span><span class="sxs-lookup"><span data-stu-id="80233-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="80233-557">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) dla katalogu głównego projektu SUT, aby umożliwić znalezienie plików statycznych i stron/widoków podczas wykonywania testów.</span><span class="sxs-lookup"><span data-stu-id="80233-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="80233-558">Udostępnia klasę [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , aby usprawnić uruchamianie SUT przy użyciu `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="80233-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="80233-559">Dokumentacja [testów jednostkowych](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) opisuje sposób konfigurowania projektu testowego i modułu testowego, a także szczegółowe instrukcje dotyczące sposobu uruchamiania testów i zaleceń dotyczących sposobu określania nazw testów i klas testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="80233-560">Podczas tworzenia projektu testowego dla aplikacji należy oddzielić testy jednostkowe od testów integracji do różnych projektów.</span><span class="sxs-lookup"><span data-stu-id="80233-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="80233-561">Dzięki temu składniki do testowania infrastruktury nie są przypadkowo uwzględniane w testach jednostkowych.</span><span class="sxs-lookup"><span data-stu-id="80233-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="80233-562">Rozdzielenie testów jednostkowych i integracyjnych umożliwia również kontrolę nad tym, który zestaw testów jest uruchamiany.</span><span class="sxs-lookup"><span data-stu-id="80233-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="80233-563">Nie istnieje praktycznie żadna różnica między konfiguracją testów Razor aplikacji stron i aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="80233-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="80233-564">Jedyną różnicą jest to, jak te testy są nazywane.</span><span class="sxs-lookup"><span data-stu-id="80233-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="80233-565">W Razor aplikacji stronicowej testy punktów końcowych stron są zwykle nazywane po klasie modelu strony (na przykład `IndexPageTests` w celu przetestowania integracji składników na stronie indeksu).</span><span class="sxs-lookup"><span data-stu-id="80233-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="80233-566">W aplikacji MVC testy są zwykle zorganizowane według klas kontrolera i nazwane po testowaniu kontrolerów (na przykład `HomeControllerTests` w celu przetestowania integracji składników dla kontrolera macierzystego).</span><span class="sxs-lookup"><span data-stu-id="80233-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="80233-567">Wymagania wstępne aplikacji testowej</span><span class="sxs-lookup"><span data-stu-id="80233-567">Test app prerequisites</span></span>

<span data-ttu-id="80233-568">Projekt testowy musi:</span><span class="sxs-lookup"><span data-stu-id="80233-568">The test project must:</span></span>

* <span data-ttu-id="80233-569">Odwołuje się do następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="80233-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="80233-570">Microsoft. AspNetCore. App</span><span class="sxs-lookup"><span data-stu-id="80233-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="80233-571">Microsoft. AspNetCore. MVC. test</span><span class="sxs-lookup"><span data-stu-id="80233-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="80233-572">Określ zestaw SDK sieci Web w pliku projektu ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).</span><span class="sxs-lookup"><span data-stu-id="80233-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="80233-573">Zestaw SDK sieci Web jest wymagany w przypadku odwoływania się do [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="80233-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="80233-574">Te wymagania wstępne można zobaczyć w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="80233-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="80233-575">Sprawdź plik *Tests/RazorPagesProject. Tests/RazorPagesProject. tests. csproj* .</span><span class="sxs-lookup"><span data-stu-id="80233-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="80233-576">Przykładowa aplikacja używa środowiska testowego [xUnit](https://xunit.github.io/) i biblioteki analizatora [AngleSharp](https://anglesharp.github.io/) , dzięki czemu Przykładowa aplikacja również odwołuje się do:</span><span class="sxs-lookup"><span data-stu-id="80233-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="80233-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="80233-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="80233-578">xUnit. Runner. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="80233-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="80233-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="80233-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="80233-580">Środowisko SUT</span><span class="sxs-lookup"><span data-stu-id="80233-580">SUT environment</span></span>

<span data-ttu-id="80233-581">Jeśli [środowisko](xref:fundamentals/environments) SUT nie jest ustawione, środowisko jest domyślnie stosowane do programowania.</span><span class="sxs-lookup"><span data-stu-id="80233-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="80233-582">Podstawowe testy z domyślną WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="80233-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="80233-583">[WebApplicationFactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) służy do tworzenia [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) dla testów integracji.</span><span class="sxs-lookup"><span data-stu-id="80233-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="80233-584">`TEntryPoint`jest klasą punktu wejścia SUT, zazwyczaj `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="80233-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="80233-585">Klasy testowe implementują interfejs *armatury klasy* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) w celu wskazania, że Klasa zawiera testy i udostępnia wystąpienia obiektów udostępnionych w ramach testów w klasie.</span><span class="sxs-lookup"><span data-stu-id="80233-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="80233-586">Poniższa klasa testowa, `BasicTests` , używa `WebApplicationFactory` do ładowania początkowego SUT i zapewnienia [HttpClient](/dotnet/api/system.net.http.httpclient) do metody testowej `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="80233-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="80233-587">Metoda sprawdza, czy kod stanu odpowiedzi powiedzie się (kody stanu z zakresu 200-299), a `Content-Type` nagłówek jest `text/html; charset=utf-8` dla kilku stron aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="80233-588">[Klient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) tworzy wystąpienie `HttpClient` , które automatycznie następuje po przekierowaniu i obsłuży pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="80233-589">Domyślnie nieistotne pliki cookie nie są zachowywane między żądaniami, gdy [zasady zgody Rodo](xref:security/gdpr) są włączone.</span><span class="sxs-lookup"><span data-stu-id="80233-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="80233-590">Aby zachować nieistotne pliki cookie, takie jak te używane przez dostawcę TempData, oznacz je jako niezbędne w testach.</span><span class="sxs-lookup"><span data-stu-id="80233-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="80233-591">Aby uzyskać instrukcje dotyczące oznaczania pliku cookie jako kluczowego, zobacz [podstawowe pliki cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="80233-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="80233-592">Dostosuj WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="80233-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="80233-593">Konfigurację hosta sieci Web można utworzyć niezależnie od klas testów przez dziedziczenie z `WebApplicationFactory` programu w celu utworzenia jednego lub kilku fabryk niestandardowych:</span><span class="sxs-lookup"><span data-stu-id="80233-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="80233-594">Dziedzicz z `WebApplicationFactory` i Zastąp [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="80233-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="80233-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umożliwia konfigurację kolekcji usług z [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), która jest wykonywana przed aplikacją `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="80233-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="80233-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) umożliwia zastępowanie i modyfikowanie zarejestrowanych usług w kolekcji usług z [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="80233-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="80233-597">Wypełnianie bazy danych w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) jest wykonywane przez `InitializeDbForTests` metodę.</span><span class="sxs-lookup"><span data-stu-id="80233-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="80233-598">Metoda jest opisana w [przykładowej testów integracji: sekcja testowa aplikacja w organizacji](#test-app-organization) .</span><span class="sxs-lookup"><span data-stu-id="80233-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="80233-599">Użyj niestandardowych `CustomWebApplicationFactory` klas testowych.</span><span class="sxs-lookup"><span data-stu-id="80233-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="80233-600">W poniższym przykładzie zastosowano fabrykę w `IndexPageTests` klasie:</span><span class="sxs-lookup"><span data-stu-id="80233-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="80233-601">Klient aplikacji przykładowej jest skonfigurowany tak, aby uniemożliwiać `HttpClient` następujące przekierowania.</span><span class="sxs-lookup"><span data-stu-id="80233-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="80233-602">Jak wyjaśniono w dalszej części sekcji [uwierzytelnianie](#mock-authentication) , pozwala to testom sprawdzić wynik pierwszej odpowiedzi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="80233-603">Pierwsza odpowiedź to przekierowanie w wielu z tych testów z `Location` nagłówkiem.</span><span class="sxs-lookup"><span data-stu-id="80233-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="80233-604">Typowy test używa `HttpClient` metod i, aby przetworzyć żądanie i odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="80233-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="80233-605">Wszelkie żądania POST do SUT muszą być zgodne z sprawdzeniem, czy jest ono automatycznie wykonywane przez [system ochrony danych przed fałszerstwem](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="80233-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="80233-606">Aby można było rozmieścić żądanie POST testu, aplikacja testowa musi:</span><span class="sxs-lookup"><span data-stu-id="80233-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="80233-607">Utwórz żądanie dla strony.</span><span class="sxs-lookup"><span data-stu-id="80233-607">Make a request for the page.</span></span>
1. <span data-ttu-id="80233-608">Przeanalizuj plik cookie dotyczący fałszowania i token walidacji żądania z odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="80233-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="80233-609">Wprowadź żądanie POST przy użyciu pliku cookie służącego do fałszerstwa i tokenu walidacji żądania.</span><span class="sxs-lookup"><span data-stu-id="80233-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="80233-610">`SendAsync`Metody rozszerzenia pomocnika (*pomocnicys/HttpClientExtensions. cs*) i `GetDocumentAsync` metoda pomocnika (*pomocnicys/HtmlHelpers. cs*) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) używają analizatora [AngleSharp](https://anglesharp.github.io/) do obsługi kontroli przed fałszerstwem przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="80233-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="80233-611">`GetDocumentAsync`&ndash;Odbiera [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) i zwraca `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="80233-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="80233-612">`GetDocumentAsync`używa fabryki przygotowującej *odpowiedź wirtualną* na podstawie oryginału `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="80233-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="80233-613">Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="80233-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="80233-614">`SendAsync`metody rozszerzające `HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) i Call [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) do przesyłania żądań do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="80233-615">Przeciążenia dla `SendAsync` Zaakceptuj formularz HTML ( `IHtmlFormElement` ) i następujące:</span><span class="sxs-lookup"><span data-stu-id="80233-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="80233-616">Przycisk przesyłania formularza ( `IHtmlElement` )</span><span class="sxs-lookup"><span data-stu-id="80233-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="80233-617">Kolekcja wartości formularza ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="80233-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="80233-618">Przycisk Prześlij ( `IHtmlElement` ) i wartości formularza ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="80233-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="80233-619">[AngleSharp](https://anglesharp.github.io/) to biblioteka analizy innej firmy używana do celów demonstracyjnych w tym temacie i Przykładowa aplikacja.</span><span class="sxs-lookup"><span data-stu-id="80233-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="80233-620">AngleSharp nie jest obsługiwana ani wymagana do testowania integracji aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80233-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="80233-621">Można użyć innych analizatorów, takich jak [pakiet HAP (html)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="80233-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="80233-622">Innym podejściem jest napisać kod, który będzie obsługiwał token weryfikacji żądań systemu i bezfałszowający plik cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="80233-623">Dostosowywanie klienta przy użyciu WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="80233-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="80233-624">Gdy w metodzie testowej jest wymagana dodatkowa konfiguracja, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) tworzy nową `WebApplicationFactory` z [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , który jest bardziej dostosowany przez konfigurację.</span><span class="sxs-lookup"><span data-stu-id="80233-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="80233-625">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`Metoda testowa [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) pokazuje użycie `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="80233-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="80233-626">Ten test służy do usuwania rekordów w bazie danych przez wyzwolenie przesłania formularza w SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="80233-627">Ponieważ inny test w `IndexPageTests` klasie wykonuje operację, która usuwa wszystkie rekordy z bazy danych i może być uruchomiona przed `Post_DeleteMessageHandler_ReturnsRedirectToRoot` metodą, baza danych zostanie oddana w tej metodzie testowej, aby upewnić się, że rekord jest obecny dla SUT do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80233-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="80233-628">Wybranie pierwszego przycisku Usuń `messages` formularza w SUT jest symulowane w żądaniu do SUT:</span><span class="sxs-lookup"><span data-stu-id="80233-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="80233-629">Opcje klienta</span><span class="sxs-lookup"><span data-stu-id="80233-629">Client options</span></span>

<span data-ttu-id="80233-630">W poniższej tabeli przedstawiono domyślne [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) dostępne podczas tworzenia `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="80233-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="80233-631">Opcja</span><span class="sxs-lookup"><span data-stu-id="80233-631">Option</span></span> | <span data-ttu-id="80233-632">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-632">Description</span></span> | <span data-ttu-id="80233-633">Domyślne</span><span class="sxs-lookup"><span data-stu-id="80233-633">Default</span></span> |
| ---
<span data-ttu-id="80233-634">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-635">'Blazor'</span></span>
- <span data-ttu-id="80233-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-636">'Identity'</span></span>
- <span data-ttu-id="80233-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-638">'Razor'</span></span>
- <span data-ttu-id="80233-639">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-639">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-640">--- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-641">'Blazor'</span></span>
- <span data-ttu-id="80233-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-642">'Identity'</span></span>
- <span data-ttu-id="80233-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-644">'Razor'</span></span>
- <span data-ttu-id="80233-645">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-646">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-647">'Blazor'</span></span>
- <span data-ttu-id="80233-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-648">'Identity'</span></span>
- <span data-ttu-id="80233-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-650">'Razor'</span></span>
- <span data-ttu-id="80233-651">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-652">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-653">'Blazor'</span></span>
- <span data-ttu-id="80233-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-654">'Identity'</span></span>
- <span data-ttu-id="80233-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-656">'Razor'</span></span>
- <span data-ttu-id="80233-657">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-657">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-658">------ | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-659">'Blazor'</span></span>
- <span data-ttu-id="80233-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-660">'Identity'</span></span>
- <span data-ttu-id="80233-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-662">'Razor'</span></span>
- <span data-ttu-id="80233-663">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-663">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Pobiera lub ustawia, czy `HttpClient` wystąpienia powinny automatycznie śledzić odpowiedzi przekierowania.</span><span class="sxs-lookup"><span data-stu-id="80233-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="80233-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Pobiera lub ustawia podstawowy adres `HttpClient` wystąpień.</span><span class="sxs-lookup"><span data-stu-id="80233-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="80233-666"> | `http://localhost`| | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Pobiera lub ustawia, czy `HttpClient` wystąpienia powinny obsługiwać pliki cookie.</span><span class="sxs-lookup"><span data-stu-id="80233-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="80233-667"> | `true`| | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Pobiera lub ustawia maksymalną liczbę odpowiedzi przekierowań, które `HttpClient` powinny być zgodne z wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="80233-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="80233-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="80233-668">| 7 |</span></span>

<span data-ttu-id="80233-669">Utwórz `WebApplicationFactoryClientOptions` klasę i przekaż ją do metody [onclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (wartości domyślne są pokazane w przykładzie kodu):</span><span class="sxs-lookup"><span data-stu-id="80233-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="80233-670">Wsuń usługi imitacji</span><span class="sxs-lookup"><span data-stu-id="80233-670">Inject mock services</span></span>

<span data-ttu-id="80233-671">Usługi można przesłaniać w teście, używając wywołania [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) na konstruktorze hosta.</span><span class="sxs-lookup"><span data-stu-id="80233-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="80233-672">**Aby wstrzyknąć usługi makiety, SUT musi mieć `Startup` klasę z `Startup.ConfigureServices` metodą.**</span><span class="sxs-lookup"><span data-stu-id="80233-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="80233-673">Przykład SUT obejmuje usługę objętą zakresem zwracającą ofertę.</span><span class="sxs-lookup"><span data-stu-id="80233-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="80233-674">Po zażądaniu strony indeksu oferta zostanie osadzona w ukrytym polu na stronie indeksu.</span><span class="sxs-lookup"><span data-stu-id="80233-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="80233-675">*Usługi/IQuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="80233-676">*Usługi/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="80233-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="80233-678">*Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="80233-679">*Strony/indeks. cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="80233-680">Po uruchomieniu aplikacji SUT jest generowany następujący znacznik:</span><span class="sxs-lookup"><span data-stu-id="80233-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="80233-681">W celu przetestowania usługi i iniekcji cytatu w teście integracji, usługa makiety jest wstrzykiwana do SUT przez test.</span><span class="sxs-lookup"><span data-stu-id="80233-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="80233-682">Usługa makiety zastępuje aplikację `QuoteService` za pomocą usługi dostarczonej przez aplikację testową o nazwie `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="80233-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="80233-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="80233-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="80233-684">`ConfigureTestServices`jest wywoływana, a usługa o określonym zakresie jest zarejestrowana:</span><span class="sxs-lookup"><span data-stu-id="80233-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="80233-685">Adiustacje powstające podczas wykonywania testu odzwierciedlają tekst cytatu dostarczony przez `TestQuoteService` , w związku z czym potwierdzenie przebiega:</span><span class="sxs-lookup"><span data-stu-id="80233-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="80233-686">Uwierzytelnianie w postaci makiety</span><span class="sxs-lookup"><span data-stu-id="80233-686">Mock authentication</span></span>

<span data-ttu-id="80233-687">Testy w `AuthTests` klasie sprawdzają, czy bezpieczny punkt końcowy:</span><span class="sxs-lookup"><span data-stu-id="80233-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="80233-688">Przekierowuje nieuwierzytelnionego użytkownika do strony logowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80233-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="80233-689">Zwraca zawartość dla uwierzytelnionego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80233-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="80233-690">W SUT `/SecurePage` Strona używa konwencji [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) w celu zastosowania [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) do strony.</span><span class="sxs-lookup"><span data-stu-id="80233-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="80233-691">Aby uzyskać więcej informacji, zobacz [ Razor strony konwencje autoryzacji](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="80233-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="80233-692">W `Get_SecurePageRedirectsAnUnauthenticatedUser` teście [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) jest ustawiony tak, aby nie zezwalać na przekierowania przez ustawienie [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) na `false` :</span><span class="sxs-lookup"><span data-stu-id="80233-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="80233-693">W przypadku niezezwalania klientowi na śledzenie przekierowania można wykonać następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="80233-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="80233-694">Kod stanu zwracany przez SUT można sprawdzić pod kątem oczekiwanego wyniku [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) , a nie do końcowego kodu stanu po przekierowaniu do strony logowania, która byłaby [HttpStatusCode. ok](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="80233-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="80233-695">`Location`Wartość nagłówka w nagłówkach odpowiedzi jest sprawdzana w celu potwierdzenia, że zaczyna się od `http://localhost/Identity/Account/Login` , a nie od końcowej odpowiedzi na stronę logowania, gdzie `Location` nagłówek nie może być obecny.</span><span class="sxs-lookup"><span data-stu-id="80233-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="80233-696">Aplikacja testowa może zasymulować <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) w programie w celu przetestowania aspektów uwierzytelniania i autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="80233-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="80233-697">Minimalny scenariusz zwraca element [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="80233-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="80233-698">`TestAuthHandler`Jest wywoływana w celu uwierzytelnienia użytkownika, gdy w schemacie uwierzytelniania jest ustawiona wartość `Test` Where `AddAuthentication` jest zarejestrowana dla `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="80233-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="80233-699">Aby uzyskać więcej informacji na temat `WebApplicationFactoryClientOptions` , zobacz sekcję [Opcje klienta](#client-options) .</span><span class="sxs-lookup"><span data-stu-id="80233-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="80233-700">Ustawianie środowiska</span><span class="sxs-lookup"><span data-stu-id="80233-700">Set the environment</span></span>

<span data-ttu-id="80233-701">Domyślnie środowisko hosta i aplikacji SUT jest skonfigurowane do korzystania ze środowiska deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="80233-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="80233-702">Aby zastąpić środowisko SUT:</span><span class="sxs-lookup"><span data-stu-id="80233-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="80233-703">Ustaw `ASPNETCORE_ENVIRONMENT` zmienną środowiskową (na przykład, `Staging` `Production` lub inną wartość niestandardową, taką jak `Testing` ).</span><span class="sxs-lookup"><span data-stu-id="80233-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="80233-704">Przesłoń `CreateWebHostBuilder` aplikację testową w celu odczytania `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="80233-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="80233-705">Środowisko można również ustawić bezpośrednio w konstruktorze hosta w niestandardowym <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="80233-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="80233-706">Jak infrastruktura testowa wnioskuje ścieżkę katalogu głównego zawartości aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="80233-707">`WebApplicationFactory`Konstruktor wnioskuje ścieżkę [katalogu głównego zawartości](xref:fundamentals/index#content-root) aplikacji, wyszukując [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) na zestawie zawierającym testy integracji z kluczem równym `TEntryPoint` zestawowi `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="80233-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="80233-708">W przypadku nieznalezienia atrybutu o poprawnym kluczu `WebApplicationFactory` Wróć do wyszukiwania pliku rozwiązania (*. sln*) i dołączaj `TEntryPoint` nazwę zestawu do katalogu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="80233-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="80233-709">Katalog główny aplikacji (ścieżka katalogu głównego zawartości) służy do odnajdywania widoków i plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="80233-710">Wyłącz kopiowanie w tle</span><span class="sxs-lookup"><span data-stu-id="80233-710">Disable shadow copying</span></span>

<span data-ttu-id="80233-711">Kopiowanie w tle powoduje, że testy są wykonywane w innym katalogu niż katalog wyjściowy.</span><span class="sxs-lookup"><span data-stu-id="80233-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="80233-712">Aby testy działały prawidłowo, kopiowanie w tle musi być wyłączone.</span><span class="sxs-lookup"><span data-stu-id="80233-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="80233-713">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) używa xUnit i wyłącza kopiowanie w tle dla xUnit, dołączając plik *xUnit. Runner. JSON* z prawidłowym ustawieniem konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="80233-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="80233-714">Aby uzyskać więcej informacji, zobacz [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="80233-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="80233-715">Dodaj plik *xUnit. Runner. JSON* do katalogu głównego projektu testowego z następującą zawartością:</span><span class="sxs-lookup"><span data-stu-id="80233-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="80233-716">Jeśli używasz programu Visual Studio, ustaw wartość właściwości **Kopiuj do katalogu wyjściowego** na **zawsze Kopiuj**.</span><span class="sxs-lookup"><span data-stu-id="80233-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="80233-717">Jeśli nie korzystasz z programu Visual Studio, Dodaj `Content` obiekt docelowy do pliku projektu aplikacji testowej:</span><span class="sxs-lookup"><span data-stu-id="80233-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="80233-718">Usuwanie obiektów</span><span class="sxs-lookup"><span data-stu-id="80233-718">Disposal of objects</span></span>

<span data-ttu-id="80233-719">Po `IClassFixture` wykonaniu testów wdrożenia [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) i [HttpClient](/dotnet/api/system.net.http.httpclient) są usuwane, gdy xUnit usuwa [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="80233-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="80233-720">Jeśli obiekty utworzone przez dewelopera wymagają usunięcia, usuń je w `IClassFixture` implementacji.</span><span class="sxs-lookup"><span data-stu-id="80233-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="80233-721">Aby uzyskać więcej informacji, zobacz [implementowanie metody Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="80233-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="80233-722">Przykład testów integracji</span><span class="sxs-lookup"><span data-stu-id="80233-722">Integration tests sample</span></span>

<span data-ttu-id="80233-723">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) składa się z dwóch aplikacji:</span><span class="sxs-lookup"><span data-stu-id="80233-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="80233-724">Aplikacja</span><span class="sxs-lookup"><span data-stu-id="80233-724">App</span></span> | <span data-ttu-id="80233-725">Katalog projektu</span><span class="sxs-lookup"><span data-stu-id="80233-725">Project directory</span></span> | <span data-ttu-id="80233-726">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-726">Description</span></span> |
| --- | ---
<span data-ttu-id="80233-727">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-728">'Blazor'</span></span>
- <span data-ttu-id="80233-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-729">'Identity'</span></span>
- <span data-ttu-id="80233-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-731">'Razor'</span></span>
- <span data-ttu-id="80233-732">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-733">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-734">'Blazor'</span></span>
- <span data-ttu-id="80233-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-735">'Identity'</span></span>
- <span data-ttu-id="80233-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-737">'Razor'</span></span>
- <span data-ttu-id="80233-738">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-739">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-740">'Blazor'</span></span>
- <span data-ttu-id="80233-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-741">'Identity'</span></span>
- <span data-ttu-id="80233-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-743">'Razor'</span></span>
- <span data-ttu-id="80233-744">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-745">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-746">'Blazor'</span></span>
- <span data-ttu-id="80233-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-747">'Identity'</span></span>
- <span data-ttu-id="80233-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-749">'Razor'</span></span>
- <span data-ttu-id="80233-750">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-751">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-752">'Blazor'</span></span>
- <span data-ttu-id="80233-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-753">'Identity'</span></span>
- <span data-ttu-id="80233-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-755">'Razor'</span></span>
- <span data-ttu-id="80233-756">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-757">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-758">'Blazor'</span></span>
- <span data-ttu-id="80233-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-759">'Identity'</span></span>
- <span data-ttu-id="80233-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-761">'Razor'</span></span>
- <span data-ttu-id="80233-762">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-762">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-763">--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-764">'Blazor'</span></span>
- <span data-ttu-id="80233-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-765">'Identity'</span></span>
- <span data-ttu-id="80233-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-767">'Razor'</span></span>
- <span data-ttu-id="80233-768">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-769">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-770">'Blazor'</span></span>
- <span data-ttu-id="80233-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-771">'Identity'</span></span>
- <span data-ttu-id="80233-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-773">'Razor'</span></span>
- <span data-ttu-id="80233-774">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-775">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-776">'Blazor'</span></span>
- <span data-ttu-id="80233-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-777">'Identity'</span></span>
- <span data-ttu-id="80233-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-779">'Razor'</span></span>
- <span data-ttu-id="80233-780">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-780">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-781">------ | | Aplikacja wiadomości (SUT) | *src/RazorPagesProject* | Zezwala użytkownikowi na dodawanie, usuwanie, usuwanie wszystkich i analizowanie komunikatów.</span><span class="sxs-lookup"><span data-stu-id="80233-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="80233-782">| | Aplikacja testowa | *testy/RazorPagesProject. Tests* | Służy do integracji testu SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="80233-783">Testy można uruchamiać przy użyciu wbudowanych funkcji testowych środowiska IDE, takich jak [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="80233-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="80233-784">W przypadku używania [Visual Studio Code](https://code.visualstudio.com/) lub wiersza polecenia wykonaj następujące polecenie w wierszu polecenia w katalogu *Tests/RazorPagesProject. Tests* :</span><span class="sxs-lookup"><span data-stu-id="80233-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="80233-785">Organizacja aplikacji wiadomości (SUT)</span><span class="sxs-lookup"><span data-stu-id="80233-785">Message app (SUT) organization</span></span>

<span data-ttu-id="80233-786">SUT to Razor system komunikatów stron o następujących cechach:</span><span class="sxs-lookup"><span data-stu-id="80233-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="80233-787">Strona indeks aplikacji (*Pages/index. cshtml* i *Pages/index. cshtml. cs*) zawiera metody interfejsu użytkownika i modelu strony umożliwiające sterowanie dodawaniem, usuwaniem i analizą komunikatów (średnia liczba wyrazów na komunikat).</span><span class="sxs-lookup"><span data-stu-id="80233-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="80233-788">Komunikat jest opisywany przez `Message` klasę (*Data/Message. cs*) z dwiema właściwościami: `Id` (Key) i `Text` (Message).</span><span class="sxs-lookup"><span data-stu-id="80233-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="80233-789">`Text`Właściwość jest wymagana i jest ograniczona do 200 znaków.</span><span class="sxs-lookup"><span data-stu-id="80233-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="80233-790">Komunikaty są przechowywane przy użyciu&#8224; [bazy danych Entity Framework w pamięci](/ef/core/providers/in-memory/) .</span><span class="sxs-lookup"><span data-stu-id="80233-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="80233-791">Aplikacja zawiera warstwę dostępu do danych (DAL) w swojej klasie kontekstu bazy danych `AppDbContext` (*Data/AppDbContext. cs*).</span><span class="sxs-lookup"><span data-stu-id="80233-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="80233-792">Jeśli baza danych jest pusta podczas uruchamiania aplikacji, magazyn komunikatów zostanie zainicjowany przy użyciu trzech komunikatów.</span><span class="sxs-lookup"><span data-stu-id="80233-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="80233-793">Aplikacja zawiera dostęp do programu `/SecurePage` , do którego jest dostępny tylko uwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="80233-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="80233-794">&#8224;temacie EF [test z niepamięcią](/ef/core/miscellaneous/testing/in-memory)— wyjaśnia, jak używać bazy danych w pamięci do testów z MSTest.</span><span class="sxs-lookup"><span data-stu-id="80233-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="80233-795">W tym temacie jest stosowane środowisko testowe [xUnit](https://xunit.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="80233-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="80233-796">Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.</span><span class="sxs-lookup"><span data-stu-id="80233-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="80233-797">Mimo że aplikacja nie korzysta ze wzorca repozytorium i nie jest skutecznym przykładem [wzorca jednostki pracy (pracownicy)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor strony programu obsługują te wzorce rozwoju.</span><span class="sxs-lookup"><span data-stu-id="80233-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="80233-798">Aby uzyskać więcej informacji, zobacz [projektowanie warstwy trwałości infrastruktury](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) i [logiki kontrolera testów](/aspnet/core/mvc/controllers/testing) (przykład implementuje wzorzec repozytorium).</span><span class="sxs-lookup"><span data-stu-id="80233-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="80233-799">Testuj organizację aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-799">Test app organization</span></span>

<span data-ttu-id="80233-800">Aplikacja testowa to Aplikacja konsolowa w katalogu *Tests/RazorPagesProject. Tests* .</span><span class="sxs-lookup"><span data-stu-id="80233-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="80233-801">Testuj katalog aplikacji</span><span class="sxs-lookup"><span data-stu-id="80233-801">Test app directory</span></span> | <span data-ttu-id="80233-802">Opis</span><span class="sxs-lookup"><span data-stu-id="80233-802">Description</span></span> |
| ---
<span data-ttu-id="80233-803">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-804">'Blazor'</span></span>
- <span data-ttu-id="80233-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-805">'Identity'</span></span>
- <span data-ttu-id="80233-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-807">'Razor'</span></span>
- <span data-ttu-id="80233-808">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-809">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-810">'Blazor'</span></span>
- <span data-ttu-id="80233-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-811">'Identity'</span></span>
- <span data-ttu-id="80233-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-813">'Razor'</span></span>
- <span data-ttu-id="80233-814">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-815">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-816">'Blazor'</span></span>
- <span data-ttu-id="80233-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-817">'Identity'</span></span>
- <span data-ttu-id="80233-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-819">'Razor'</span></span>
- <span data-ttu-id="80233-820">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-821">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-822">'Blazor'</span></span>
- <span data-ttu-id="80233-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-823">'Identity'</span></span>
- <span data-ttu-id="80233-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-825">'Razor'</span></span>
- <span data-ttu-id="80233-826">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-827">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-828">'Blazor'</span></span>
- <span data-ttu-id="80233-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-829">'Identity'</span></span>
- <span data-ttu-id="80233-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-831">'Razor'</span></span>
- <span data-ttu-id="80233-832">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-833">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-834">'Blazor'</span></span>
- <span data-ttu-id="80233-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-835">'Identity'</span></span>
- <span data-ttu-id="80233-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-837">'Razor'</span></span>
- <span data-ttu-id="80233-838">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-839">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-840">'Blazor'</span></span>
- <span data-ttu-id="80233-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-841">'Identity'</span></span>
- <span data-ttu-id="80233-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-843">'Razor'</span></span>
- <span data-ttu-id="80233-844">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-844">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-845">--------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-846">'Blazor'</span></span>
- <span data-ttu-id="80233-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-847">'Identity'</span></span>
- <span data-ttu-id="80233-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-849">'Razor'</span></span>
- <span data-ttu-id="80233-850">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-851">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-852">'Blazor'</span></span>
- <span data-ttu-id="80233-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-853">'Identity'</span></span>
- <span data-ttu-id="80233-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-855">'Razor'</span></span>
- <span data-ttu-id="80233-856">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="80233-857">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="80233-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="80233-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="80233-858">'Blazor'</span></span>
- <span data-ttu-id="80233-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="80233-859">'Identity'</span></span>
- <span data-ttu-id="80233-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="80233-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="80233-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="80233-861">'Razor'</span></span>
- <span data-ttu-id="80233-862">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="80233-862">'SignalR' uid:</span></span> 

<span data-ttu-id="80233-863">------ | | *AuthTests* | Zawiera metody testowe dla:</span><span class="sxs-lookup"><span data-stu-id="80233-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="80233-864">Uzyskiwanie dostępu do bezpiecznej strony przez nieuwierzytelniony użytkownik.</span><span class="sxs-lookup"><span data-stu-id="80233-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="80233-865">Uzyskiwanie dostępu do bezpiecznej strony przez uwierzytelnionego użytkownika przy użyciu makiety <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="80233-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="80233-866">Uzyskiwanie profilu użytkownika usługi GitHub i sprawdzanie logowania użytkownika w profilu.</span><span class="sxs-lookup"><span data-stu-id="80233-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="80233-867">| | *BasicTests* | Zawiera metodę testową dla routingu i typu zawartości.</span><span class="sxs-lookup"><span data-stu-id="80233-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="80233-868">| | *IntegrationTests* | Zawiera testy integracji dla strony indeksu przy użyciu klasy niestandardowej `WebApplicationFactory` .</span><span class="sxs-lookup"><span data-stu-id="80233-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="80233-869">| | *Pomocnicy/narzędzia* | </span><span class="sxs-lookup"><span data-stu-id="80233-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="80233-870">*Utilities.cs* zawiera `InitializeDbForTests` metodę używaną do wypełniania bazy danych danymi testowymi.</span><span class="sxs-lookup"><span data-stu-id="80233-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="80233-871">*HtmlHelpers.cs* zapewnia metodę, która zwraca AngleSharp `IHtmlDocument` do użycia przez metody testowe.</span><span class="sxs-lookup"><span data-stu-id="80233-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="80233-872">*HttpClientExtensions.cs* zapewniają przeciążenia dla programu, `SendAsync` Aby przesyłać żądania do SUT.</span><span class="sxs-lookup"><span data-stu-id="80233-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="80233-873">Platforma testowa jest [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="80233-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="80233-874">Testy integracji są przeprowadzane przy użyciu [programu Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), który obejmuje [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="80233-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="80233-875">Ponieważ pakiet [Microsoft. AspNetCore. MVC. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) jest używany do konfigurowania hosta testowego i serwera testowego, `TestHost` pakiety i `TestServer` nie wymagają bezpośrednich odwołań do pakietów w pliku projektu aplikacji testowej ani w konfiguracji dewelopera w aplikacji testowej.</span><span class="sxs-lookup"><span data-stu-id="80233-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="80233-876">**Umieszczanie bazy danych do testowania**</span><span class="sxs-lookup"><span data-stu-id="80233-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="80233-877">Testy integracji zwykle wymagają małego zestawu danych w bazie danych przed wykonaniem testu.</span><span class="sxs-lookup"><span data-stu-id="80233-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="80233-878">Na przykład można usunąć wywołania testu do usuwania rekordów bazy danych, więc baza danych musi mieć co najmniej jeden rekord, aby żądanie usunięcia zakończyło się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="80233-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="80233-879">Przykładowa aplikacja odziarnauje bazę danych z trzema komunikatami w *Utilities.cs* , że testy mogą być używane podczas wykonywania:</span><span class="sxs-lookup"><span data-stu-id="80233-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="80233-880">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="80233-880">Additional resources</span></span>

* [<span data-ttu-id="80233-881">Testy jednostkowe</span><span class="sxs-lookup"><span data-stu-id="80233-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
