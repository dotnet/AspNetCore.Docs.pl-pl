---
title: Omówienie platformy ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak ASP.NET Core MVC to rozbudowana platforma służąca do tworzenia aplikacji sieci Web i interfejsów API przy użyciu wzorca projektowego modelu widoku.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 64bee1966eff40e9057166adfafbf78d0cc5b6aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021448"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="531b0-103">Omówienie platformy ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="531b0-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="531b0-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="531b0-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="531b0-105">ASP.NET Core MVC to rozbudowana platforma służąca do tworzenia aplikacji sieci Web i interfejsów API przy użyciu wzorca projektowego modelu widoku.</span><span class="sxs-lookup"><span data-stu-id="531b0-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="531b0-106">Co to jest wzorzec MVC?</span><span class="sxs-lookup"><span data-stu-id="531b0-106">What is the MVC pattern?</span></span>

<span data-ttu-id="531b0-107">Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych grup składników: modeli, widoków i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="531b0-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="531b0-108">Ten wzorzec pozwala uzyskać [rozdzielenie obaw](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="531b0-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="531b0-109">Korzystając z tego wzorca, żądania użytkowników są kierowane do kontrolera, który jest odpowiedzialny za pracę z modelem w celu wykonywania akcji użytkownika i/lub pobierania wyników zapytań.</span><span class="sxs-lookup"><span data-stu-id="531b0-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="531b0-110">Kontroler wybierze widok, który ma być wyświetlany użytkownikowi, i udostępnia mu wymagane dane modelu.</span><span class="sxs-lookup"><span data-stu-id="531b0-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="531b0-111">Na poniższym diagramie przedstawiono trzy główne składniki, które odwołują się do innych:</span><span class="sxs-lookup"><span data-stu-id="531b0-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Wzorzec MVC](overview/_static/mvc.png)

<span data-ttu-id="531b0-113">Ten podział obowiązków pomaga skalować aplikację pod kątem złożoności, ponieważ ułatwia to kod, debugowanie i testowanie elementu (model, widok lub kontroler), który ma pojedyncze zadanie.</span><span class="sxs-lookup"><span data-stu-id="531b0-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="531b0-114">Jest trudniejsze, aby aktualizować, testować i debugować kod, który ma zależności rozłożone na dwa lub więcej z tych trzech obszarów.</span><span class="sxs-lookup"><span data-stu-id="531b0-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="531b0-115">Na przykład logika interfejsu użytkownika zmienia się częściej niż logika biznesowa.</span><span class="sxs-lookup"><span data-stu-id="531b0-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="531b0-116">Jeśli kod prezentacji i logika biznesowa są łączone w pojedynczym obiekcie, obiekt zawierający logikę biznesową musi być modyfikowany za każdym razem, gdy interfejs użytkownika jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="531b0-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="531b0-117">Często wprowadza błędy i wymaga przetestowania logiki biznesowej po każdym minimalnym zmianie interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="531b0-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="531b0-118">Zarówno widok, jak i kontroler zależą od modelu.</span><span class="sxs-lookup"><span data-stu-id="531b0-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="531b0-119">Jednak model jest zależny od widoku, a nie kontrolera.</span><span class="sxs-lookup"><span data-stu-id="531b0-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="531b0-120">Jest to jedna z najważniejszych zalet separacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="531b0-121">Ta separacja umożliwia skompilowanie i przetestowanie modelu niezależnie od prezentacji wizualnej.</span><span class="sxs-lookup"><span data-stu-id="531b0-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="531b0-122">Obowiązki związane z modelem</span><span class="sxs-lookup"><span data-stu-id="531b0-122">Model Responsibilities</span></span>

<span data-ttu-id="531b0-123">Model w aplikacji MVC reprezentuje stan aplikacji i wszelkie operacje logiki biznesowej, które powinny być wykonywane przez nią.</span><span class="sxs-lookup"><span data-stu-id="531b0-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="531b0-124">Logika biznesowa powinna być hermetyzowana w modelu wraz z dowolnymi regułami implementacji dotyczącymi utrwalania stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="531b0-125">Widoki z jednoznacznie określonymi typami zazwyczaj używają typów ViewModel zaprojektowanych do przechowywania danych do wyświetlenia w tym widoku.</span><span class="sxs-lookup"><span data-stu-id="531b0-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="531b0-126">Kontroler tworzy i wypełnia te wystąpienia ViewModel z modelu.</span><span class="sxs-lookup"><span data-stu-id="531b0-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="531b0-127">Wyświetl obowiązki</span><span class="sxs-lookup"><span data-stu-id="531b0-127">View Responsibilities</span></span>

<span data-ttu-id="531b0-128">Widoki są odpowiedzialne za prezentowanie zawartości za pomocą interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="531b0-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="531b0-129">Używają one [ Razor aparatu widoku](#razor-view-engine) do osadzania kodu platformy .NET w znaczniku html.</span><span class="sxs-lookup"><span data-stu-id="531b0-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="531b0-130">W widokach powinna być minimalna logika, a jakakolwiek logika powinna odnosić się do treści prezentacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="531b0-131">Jeśli okaże się, że trzeba wykonać znaczną transakcję logiki w plikach widoku, aby wyświetlić dane z modelu złożonego, należy rozważyć użycie [składnika widoku](views/view-components.md), ViewModel lub szablonu widoku, aby uprościć widok.</span><span class="sxs-lookup"><span data-stu-id="531b0-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="531b0-132">Obowiązki kontrolera</span><span class="sxs-lookup"><span data-stu-id="531b0-132">Controller Responsibilities</span></span>

<span data-ttu-id="531b0-133">Kontrolery są składnikami, które obsługują interakcję z użytkownikiem, pracują z modelem i ostatecznie wybierają widok do renderowania.</span><span class="sxs-lookup"><span data-stu-id="531b0-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="531b0-134">W aplikacji MVC widok wyświetla tylko informacje; kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje.</span><span class="sxs-lookup"><span data-stu-id="531b0-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="531b0-135">We wzorcu MVC kontroler jest punktem wejścia, który jest odpowiedzialny za wybór typów modelu do pracy i widok do renderowania (w związku z czym jego nazwa określa, jak aplikacja odpowiada na daną prośbę).</span><span class="sxs-lookup"><span data-stu-id="531b0-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="531b0-136">Kontrolery nie powinny być nadmiernie skomplikowane przez zbyt wiele obowiązków.</span><span class="sxs-lookup"><span data-stu-id="531b0-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="531b0-137">Aby zachować logikę kontrolera przed nadmierną złożonością, wypychanie logiki biznesowej z kontrolera i do modelu domeny.</span><span class="sxs-lookup"><span data-stu-id="531b0-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="531b0-138">Jeśli okaże się, że akcje wykonywane przez kontroler często wykonują tego samego rodzaju akcje, przenieś te typowe akcje do [filtrów](#filters).</span><span class="sxs-lookup"><span data-stu-id="531b0-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="531b0-139">Co to jest ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="531b0-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="531b0-140">ASP.NET Core MVC Framework jest lekkim, wysoce weryfikowalne środowiskiem prezentacji zoptymalizowanym pod kątem używania z ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="531b0-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="531b0-141">ASP.NET Core MVC oferuje oparty na wzorcach sposób tworzenia dynamicznych witryn sieci Web, które umożliwiają czyste rozdzielenie problemów.</span><span class="sxs-lookup"><span data-stu-id="531b0-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="531b0-142">Zapewnia pełną kontrolę nad znacznikiem, obsługuje programowanie przyjaznych dla TDD i używa najnowszych standardów sieci Web.</span><span class="sxs-lookup"><span data-stu-id="531b0-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="531b0-143">Funkcje</span><span class="sxs-lookup"><span data-stu-id="531b0-143">Features</span></span>

<span data-ttu-id="531b0-144">ASP.NET Core MVC obejmuje następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="531b0-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="531b0-145">Routing</span><span class="sxs-lookup"><span data-stu-id="531b0-145">Routing</span></span>](#routing)
* [<span data-ttu-id="531b0-146">Powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="531b0-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="531b0-147">Walidacja modelu</span><span class="sxs-lookup"><span data-stu-id="531b0-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="531b0-148">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="531b0-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="531b0-149">Filtry</span><span class="sxs-lookup"><span data-stu-id="531b0-149">Filters</span></span>](#filters)
* [<span data-ttu-id="531b0-150">Obszary</span><span class="sxs-lookup"><span data-stu-id="531b0-150">Areas</span></span>](#areas)
* [<span data-ttu-id="531b0-151">Interfejsy API sieci Web</span><span class="sxs-lookup"><span data-stu-id="531b0-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="531b0-152">Testowalności</span><span class="sxs-lookup"><span data-stu-id="531b0-152">Testability</span></span>](#testability)
* [<span data-ttu-id="531b0-153">RazorWyświetl aparat</span><span class="sxs-lookup"><span data-stu-id="531b0-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="531b0-154">Widoki o jednoznacznie określonym typie</span><span class="sxs-lookup"><span data-stu-id="531b0-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="531b0-155">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="531b0-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="531b0-156">Wyświetl składniki</span><span class="sxs-lookup"><span data-stu-id="531b0-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="531b0-157">Routing</span><span class="sxs-lookup"><span data-stu-id="531b0-157">Routing</span></span>

<span data-ttu-id="531b0-158">ASP.NET Core MVC jest oparty na [routingu ASP.NET Core](../fundamentals/routing.md), czyli zaawansowanym składniku mapowania adresów URL, który umożliwia tworzenie aplikacji, które mają zrozumiały i przeszukiwany adres URL.</span><span class="sxs-lookup"><span data-stu-id="531b0-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="531b0-159">Pozwala to definiować wzorce nazewnictwa adresów URL aplikacji, które dobrze sprawdzają się w przypadku optymalizacji aparatu wyszukiwania (wyszukiwarki) i generowania linków, bez względu na sposób organizowania plików na serwerze sieci Web.</span><span class="sxs-lookup"><span data-stu-id="531b0-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="531b0-160">Trasy można definiować przy użyciu wygodnej składni szablonu trasy, która obsługuje ograniczenia wartości tras, wartości domyślne i opcjonalne.</span><span class="sxs-lookup"><span data-stu-id="531b0-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="531b0-161">*Routing oparty na Konwencji* umożliwia globalne Definiowanie formatów adresów URL akceptowanych przez aplikację oraz sposobu mapowania poszczególnych formatów na określoną metodę akcji na danym kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="531b0-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="531b0-162">Po odebraniu żądania przychodzącego aparat routingu analizuje adres URL i dopasowuje go do jednego ze zdefiniowanych formatów adresów URL, a następnie wywołuje metodę akcji skojarzonego kontrolera.</span><span class="sxs-lookup"><span data-stu-id="531b0-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="531b0-163">*Routing atrybutu* umożliwia określenie informacji o routingu przez dekorowania nazwy kontrolerów i akcji przy użyciu atrybutów, które definiują trasy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="531b0-164">Oznacza to, że definicje tras są umieszczane obok kontrolera i akcji, z którymi są skojarzone.</span><span class="sxs-lookup"><span data-stu-id="531b0-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a><span data-ttu-id="531b0-165">Powiązanie modelu</span><span class="sxs-lookup"><span data-stu-id="531b0-165">Model binding</span></span>

<span data-ttu-id="531b0-166">ASP.NET Core [powiązanie modelu](models/model-binding.md) MVC konwertuje dane żądania klienta (wartości formularza, dane tras, parametry ciągu zapytania, nagłówki HTTP) na obiekty, które może obsłużyć kontroler.</span><span class="sxs-lookup"><span data-stu-id="531b0-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="531b0-167">W związku z tym logika kontrolera nie musi wykonywać zadań, aby wyrównać dane żądania przychodzącego; po prostu zawiera dane jako parametry do metod akcji.</span><span class="sxs-lookup"><span data-stu-id="531b0-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="531b0-168">Walidacja modelu</span><span class="sxs-lookup"><span data-stu-id="531b0-168">Model validation</span></span>

<span data-ttu-id="531b0-169">ASP.NET Core MVC obsługuje [walidację](models/validation.md) przez dekorowania nazwy obiektu modelu z atrybutami walidacji adnotacji danych.</span><span class="sxs-lookup"><span data-stu-id="531b0-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="531b0-170">Atrybuty walidacji są sprawdzane po stronie klienta, zanim wartości są ogłaszane na serwerze, a także na serwerze przed wywołaniem akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="531b0-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="531b0-171">Akcja kontrolera:</span><span class="sxs-lookup"><span data-stu-id="531b0-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="531b0-172">Platforma obsługuje walidację danych żądania zarówno na kliencie, jak i na serwerze.</span><span class="sxs-lookup"><span data-stu-id="531b0-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="531b0-173">Logika walidacji określona w typach modeli jest dodawana do renderowanych widoków jako niedyskretne adnotacje i wymuszane w przeglądarce przy użyciu [walidacji jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="531b0-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="531b0-174">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="531b0-174">Dependency injection</span></span>

<span data-ttu-id="531b0-175">ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="531b0-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="531b0-176">W ASP.NET Core MVC [Kontrolery](controllers/dependency-injection.md) mogą zażądać wymaganych usług za pomocą ich konstruktorów, umożliwiając im przestrzeganie [zasad jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="531b0-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="531b0-177">Twoja aplikacja może również używać [iniekcji zależności w plikach widoku](views/dependency-injection.md)przy użyciu `@inject` dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="531b0-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="531b0-178">Filtry</span><span class="sxs-lookup"><span data-stu-id="531b0-178">Filters</span></span>

<span data-ttu-id="531b0-179">[Filtry](controllers/filters.md) ułatwiają deweloperom hermetyzację zagadnień związanych z zmniejszeniem, takich jak obsługa wyjątków czy autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="531b0-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="531b0-180">Filtry umożliwiają uruchamianie niestandardowej logiki sprzed i po przetworzeniu dla metod akcji i można ją skonfigurować do uruchamiania w określonych punktach w potoku wykonywania dla danego żądania.</span><span class="sxs-lookup"><span data-stu-id="531b0-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="531b0-181">Filtry mogą być stosowane do kontrolerów lub akcji jako atrybuty (lub mogą być uruchamiane globalnie).</span><span class="sxs-lookup"><span data-stu-id="531b0-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="531b0-182">W strukturze uwzględniono kilka filtrów (takich jak `Authorize` ).</span><span class="sxs-lookup"><span data-stu-id="531b0-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="531b0-183">`[Authorize]`jest atrybutem używanym do tworzenia filtrów autoryzacji MVC.</span><span class="sxs-lookup"><span data-stu-id="531b0-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="531b0-184">Obszary</span><span class="sxs-lookup"><span data-stu-id="531b0-184">Areas</span></span>

<span data-ttu-id="531b0-185">[Obszary](controllers/areas.md) umożliwiają partycjonowanie dużej aplikacji sieci Web MVC ASP.NET Core w mniejszych grupach funkcjonalnych.</span><span class="sxs-lookup"><span data-stu-id="531b0-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="531b0-186">Obszar jest strukturą MVC wewnątrz aplikacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="531b0-187">W projekcie MVC składniki logiczne, takie jak model, kontroler i widok, są przechowywane w różnych folderach, a MVC używają konwencji nazewnictwa, aby utworzyć relację między tymi składnikami.</span><span class="sxs-lookup"><span data-stu-id="531b0-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="531b0-188">W przypadku dużej aplikacji warto podzielić aplikację na oddzielne obszary wysokiego poziomu funkcji.</span><span class="sxs-lookup"><span data-stu-id="531b0-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="531b0-189">Na przykład aplikacja handlu elektronicznego z wieloma jednostkami biznesowymi, takimi jak wyewidencjonowywanie, rozliczenia i wyszukiwanie, itp. Każda z tych jednostek ma własne widoki, kontrolery i modele logicznego składnika.</span><span class="sxs-lookup"><span data-stu-id="531b0-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="531b0-190">Interfejsy API sieci Web</span><span class="sxs-lookup"><span data-stu-id="531b0-190">Web APIs</span></span>

<span data-ttu-id="531b0-191">Poza doskonałym platformą do tworzenia witryn sieci Web, ASP.NET Core MVC ma doskonałą obsługę tworzenia interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="531b0-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="531b0-192">Możesz tworzyć usługi, które docierają do szerokiego zakresu klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="531b0-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="531b0-193">Platforma obejmuje obsługę negocjacji zawartości HTTP z wbudowaną obsługą [formatowania danych](xref:web-api/advanced/formatting) jako JSON lub XML.</span><span class="sxs-lookup"><span data-stu-id="531b0-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="531b0-194">Napisz [niestandardowe elementy formatujące](xref:web-api/advanced/custom-formatters) , aby dodać obsługę własnych formatów.</span><span class="sxs-lookup"><span data-stu-id="531b0-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="531b0-195">Użyj generowania linków, aby włączyć obsługę multimediów.</span><span class="sxs-lookup"><span data-stu-id="531b0-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="531b0-196">Łatwo Włącz obsługę [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/) , aby interfejsy API sieci Web mogły być współużytkowane przez wiele aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="531b0-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="531b0-197">Testowalności</span><span class="sxs-lookup"><span data-stu-id="531b0-197">Testability</span></span>

<span data-ttu-id="531b0-198">Korzystanie z interfejsów i iniekcja zależności umożliwia odpowiednie rozwiązanie do testowania jednostkowego, a platforma obejmuje funkcje (takie jak TestHost i Dostawca pamięci dla Entity Framework), które umożliwiają szybkie i łatwe testowanie [integracji](xref:test/integration-tests) .</span><span class="sxs-lookup"><span data-stu-id="531b0-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="531b0-199">Dowiedz się więcej [na temat testowania logiki kontrolera](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="531b0-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="531b0-200">RazorWyświetl aparat</span><span class="sxs-lookup"><span data-stu-id="531b0-200">Razor view engine</span></span>

<span data-ttu-id="531b0-201">[ASP.NET Core widoki MVC](views/overview.md) wykorzystują [ Razor aparat widoku](views/razor.md) do renderowania widoków.</span><span class="sxs-lookup"><span data-stu-id="531b0-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="531b0-202">Razorto zwarty, wyraźny i płynny język znaczników szablonów do definiowania widoków przy użyciu osadzonego kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="531b0-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="531b0-203">Razorsłuży do dynamicznego generowania zawartości sieci Web na serwerze.</span><span class="sxs-lookup"><span data-stu-id="531b0-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="531b0-204">Można wyczyścić kod serwera z zawartością i kodem po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="531b0-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="531b0-205">Korzystając z Razor aparatu widoku, można definiować [układy](views/layout.md), [częściowe widoki](views/partial.md) i przemieścić sekcje.</span><span class="sxs-lookup"><span data-stu-id="531b0-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="531b0-206">Widoki o jednoznacznie określonym typie</span><span class="sxs-lookup"><span data-stu-id="531b0-206">Strongly typed views</span></span>

<span data-ttu-id="531b0-207">Razorwidoki w MVC można jednoznacznie wpisywać na podstawie modelu.</span><span class="sxs-lookup"><span data-stu-id="531b0-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="531b0-208">Kontrolery mogą przekazać silnie wpisany model do widoków, co umożliwia kontrolowanie typów i obsługę technologii IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="531b0-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="531b0-209">Na przykład następujący widok renderuje model typu `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="531b0-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="531b0-210">Pomocnicy tagów</span><span class="sxs-lookup"><span data-stu-id="531b0-210">Tag Helpers</span></span>

<span data-ttu-id="531b0-211">[Pomocnicy tagów](views/tag-helpers/intro.md) Włącz kod po stronie serwera, aby wziąć udział w tworzeniu i RENDEROWANIU elementów HTML w Razor plikach.</span><span class="sxs-lookup"><span data-stu-id="531b0-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="531b0-212">Za pomocą pomocników tagów można definiować niestandardowe znaczniki (na przykład `<environment>` ) lub modyfikować zachowanie istniejących tagów (na przykład `<label>` ).</span><span class="sxs-lookup"><span data-stu-id="531b0-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="531b0-213">Pomocnicy tagów powiążą się z określonymi elementami na podstawie nazwy elementu i jego atrybutów.</span><span class="sxs-lookup"><span data-stu-id="531b0-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="531b0-214">Zapewniają one zalety renderowania po stronie serwera, zachowując jednocześnie środowisko edycji HTML.</span><span class="sxs-lookup"><span data-stu-id="531b0-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="531b0-215">Istnieje wiele wbudowanych pomocników tagów dla typowych zadań, takich jak tworzenie formularzy, linków, ładowanie zasobów i inne — a nawet więcej dostępnych w publicznych repozytoriach GitHub i jako pakiety NuGet.</span><span class="sxs-lookup"><span data-stu-id="531b0-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="531b0-216">Pomocnicy tagów są twórcy w języku C# i są elementami docelowymi elementów HTML w oparciu o nazwę elementu, nazwę atrybutu lub tag nadrzędny.</span><span class="sxs-lookup"><span data-stu-id="531b0-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="531b0-217">Na przykład wbudowana LinkTagHelper może służyć do tworzenia linku do `Login` akcji `AccountsController` :</span><span class="sxs-lookup"><span data-stu-id="531b0-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="531b0-218">`EnvironmentTagHelper`Może służyć do uwzględnienia różnych skryptów w widokach (na przykład Raw lub zminimalizowanego) w oparciu o środowisko uruchomieniowe, takie jak programowanie, przemieszczanie lub produkcja:</span><span class="sxs-lookup"><span data-stu-id="531b0-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="531b0-219">Pomocnicy tagów zapewniają przyjazne dla języka HTML środowisko programistyczne i zaawansowane środowisko IntelliSense do tworzenia kodu HTML i Razor znaczników.</span><span class="sxs-lookup"><span data-stu-id="531b0-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="531b0-220">Większość wbudowanych pomocników tagów docelowo istniejące elementy HTML i udostępniają atrybuty po stronie serwera dla elementu.</span><span class="sxs-lookup"><span data-stu-id="531b0-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="531b0-221">Wyświetl składniki</span><span class="sxs-lookup"><span data-stu-id="531b0-221">View Components</span></span>

<span data-ttu-id="531b0-222">[Składniki widoku](views/view-components.md) umożliwiają pakowanie logiki renderowania i ponowne używanie jej w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="531b0-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="531b0-223">Są one podobne do [widoków częściowych](views/partial.md), ale ze skojarzoną logiką.</span><span class="sxs-lookup"><span data-stu-id="531b0-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="531b0-224">Wersja zgodności</span><span class="sxs-lookup"><span data-stu-id="531b0-224">Compatibility version</span></span>

<span data-ttu-id="531b0-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Metoda pozwala aplikacji na zgodę lub rezygnację z ewentualnych zmian w zachowaniu, wprowadzonych w ASP.NET Core MVC 2,1 lub nowszych.</span><span class="sxs-lookup"><span data-stu-id="531b0-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="531b0-226">Aby uzyskać więcej informacji, zobacz <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="531b0-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="531b0-227">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="531b0-227">Additional resources</span></span>

* <span data-ttu-id="531b0-228">[AspNetCore. MVC-Fluent — Biblioteka testów dla ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Biblioteka testów jednostkowych z jednoznacznie określonymi typami, dostarczająca interfejs Fluent do testowania aplikacji MVC i Web API.</span><span class="sxs-lookup"><span data-stu-id="531b0-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="531b0-229">(*Niekonserwowane lub obsługiwane przez firmę Microsoft).*</span><span class="sxs-lookup"><span data-stu-id="531b0-229">(*Not maintained or supported by Microsoft.*)</span></span>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>
