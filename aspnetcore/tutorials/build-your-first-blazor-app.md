---
title: Tworzenie pierwszej Blazor aplikacji
author: guardrex
description: Tworzenie Blazor aplikacji krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
uid: tutorials/first-blazor-app
ms.openlocfilehash: 138057c2ceb9ed01bdf958c01f5cf2275387df23
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989431"
---
# <a name="build-your-first-opno-locblazor-app"></a><span data-ttu-id="a49cc-103">Tworzenie pierwszej Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="a49cc-103">Build your first Blazor app</span></span>

<span data-ttu-id="a49cc-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a49cc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a49cc-105">W tym samouczku pokazano, Blazor jak skompilować i zmodyfikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="a49cc-106">Buduj komponenty</span><span class="sxs-lookup"><span data-stu-id="a49cc-106">Build components</span></span>

1. <span data-ttu-id="a49cc-107">Postępuj zgodnie <xref:blazor/get-started> ze wskazówkami Blazor zawartymi w artykule, aby utworzyć projekt dla tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="a49cc-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="a49cc-108">Nazwij projekt *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="a49cc-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="a49cc-109">Przejdź do każdej z trzech stron aplikacji w folderze *Strony:* Dane główne, Licznik i Pobieranie.</span><span class="sxs-lookup"><span data-stu-id="a49cc-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="a49cc-110">Strony te są realizowane przez pliki składowe Razor *Index.brzytwa*, *Counter.brzytwa*i *FetchData.brzytwa.*</span><span class="sxs-lookup"><span data-stu-id="a49cc-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="a49cc-111">Na stronie Licznik wybierz przycisk **Kliknij mnie,** aby zwiększać licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="a49cc-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a49cc-112">Zwiększanie licznika na stronie sieci Web zwykle wymaga pisania javascript.</span><span class="sxs-lookup"><span data-stu-id="a49cc-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="a49cc-113">Za Blazorpomocą , można napisać C# zamiast.</span><span class="sxs-lookup"><span data-stu-id="a49cc-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="a49cc-114">Sprawdź implementację `Counter` składnika w pliku *Counter.brzytwa.*</span><span class="sxs-lookup"><span data-stu-id="a49cc-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="a49cc-115">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="a49cc-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="a49cc-116">Interfejs użytkownika składnika `Counter` jest zdefiniowany przy użyciu kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="a49cc-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="a49cc-117">Dynamiczna logika renderowania (na przykład pętle, warunki, wyrażenia) jest dodawana przy użyciu osadzonej składni języka C# o nazwie [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="a49cc-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="a49cc-118">Logika renderowania znaczników HTML i C# są konwertowane na klasę składników w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="a49cc-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="a49cc-119">Nazwa wygenerowanej klasy .NET jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="a49cc-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="a49cc-120">Elementy członkowskie klasy komponentu `@code` są definiowane w bloku.</span><span class="sxs-lookup"><span data-stu-id="a49cc-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="a49cc-121">W `@code` bloku stan składnika (właściwości, pola) i metody są określone dla obsługi zdarzeń lub do definiowania innych logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="a49cc-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="a49cc-122">Te elementy członkowskie są następnie używane jako część logiki renderowania składnika i do obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a49cc-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="a49cc-123">Po wybraniu przycisku **Kliknij mnie:**</span><span class="sxs-lookup"><span data-stu-id="a49cc-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="a49cc-124">Wywoływany `Counter` jest `onclick` program obsługi zarejestrowany `IncrementCount` przez składnik (metoda).</span><span class="sxs-lookup"><span data-stu-id="a49cc-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="a49cc-125">Składnik `Counter` regeneruje jego drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="a49cc-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="a49cc-126">Nowe drzewo renderowania jest porównywane z poprzednim.</span><span class="sxs-lookup"><span data-stu-id="a49cc-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="a49cc-127">Stosowane są tylko modyfikacje modelu obiektowego dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="a49cc-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="a49cc-128">Wyświetlana liczba zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="a49cc-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="a49cc-129">Zmodyfikuj `Counter` logikę C# składnika, aby liczba zwiększała się o dwa zamiast o jeden.</span><span class="sxs-lookup"><span data-stu-id="a49cc-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="a49cc-130">Odbuduj i uruchom aplikację, aby zobaczyć zmiany.</span><span class="sxs-lookup"><span data-stu-id="a49cc-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="a49cc-131">Wybierz przycisk **Kliknij mnie.**</span><span class="sxs-lookup"><span data-stu-id="a49cc-131">Select the **Click me** button.</span></span> <span data-ttu-id="a49cc-132">Licznik zwiększa się o dwa.</span><span class="sxs-lookup"><span data-stu-id="a49cc-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="a49cc-133">Użyj komponentów</span><span class="sxs-lookup"><span data-stu-id="a49cc-133">Use components</span></span>

<span data-ttu-id="a49cc-134">Dołącz składnik do innego składnika przy użyciu składni HTML.</span><span class="sxs-lookup"><span data-stu-id="a49cc-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="a49cc-135">Dodaj `Counter` składnik do składnika `Index` aplikacji, `<Counter />` dodając element `Index` do składnika *(Index.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="a49cc-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="a49cc-136">Jeśli używasz Blazor WebAssembly dla tego `SurveyPrompt` środowiska, składnik `Index` jest używany przez składnik.</span><span class="sxs-lookup"><span data-stu-id="a49cc-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="a49cc-137">Zamień `<SurveyPrompt>` element `<Counter />` na element.</span><span class="sxs-lookup"><span data-stu-id="a49cc-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="a49cc-138">Jeśli używasz aplikacji Blazor serwera dla tego środowiska, `<Counter />` dodaj `Index` element do składnika:</span><span class="sxs-lookup"><span data-stu-id="a49cc-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="a49cc-139">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="a49cc-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="a49cc-140">Odbuduj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-140">Rebuild and run the app.</span></span> <span data-ttu-id="a49cc-141">Składnik `Index` ma swój własny licznik.</span><span class="sxs-lookup"><span data-stu-id="a49cc-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="a49cc-142">Parametry komponentu</span><span class="sxs-lookup"><span data-stu-id="a49cc-142">Component parameters</span></span>

<span data-ttu-id="a49cc-143">Komponenty mogą również mieć parametry.</span><span class="sxs-lookup"><span data-stu-id="a49cc-143">Components can also have parameters.</span></span> <span data-ttu-id="a49cc-144">Parametry komponentu są definiowane przy użyciu `[Parameter]` właściwości publicznych w klasie składnika z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="a49cc-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="a49cc-145">Atrybuty służy do określania argumentów dla składnika w znacznikach.</span><span class="sxs-lookup"><span data-stu-id="a49cc-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="a49cc-146">Zaktualizuj `@code` kod C# składnika w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a49cc-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="a49cc-147">Dodaj właściwość publiczną `IncrementAmount` z atrybutem. `[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="a49cc-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="a49cc-148">Zmień `IncrementCount` metodę, aby `IncrementAmount` użyć właściwości podczas `currentCount`zwiększania wartości .</span><span class="sxs-lookup"><span data-stu-id="a49cc-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="a49cc-149">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="a49cc-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="a49cc-150">Określ `IncrementAmount` parametr `Index` w elemencie składnika `<Counter>` przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a49cc-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="a49cc-151">Ustaw wartość, aby zwiększać licznik o dziesięć.</span><span class="sxs-lookup"><span data-stu-id="a49cc-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="a49cc-152">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="a49cc-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="a49cc-153">Przeładuj `Index` ponownie komponent.</span><span class="sxs-lookup"><span data-stu-id="a49cc-153">Reload the `Index` component.</span></span> <span data-ttu-id="a49cc-154">Licznik zwiększa się o dziesięć za każdym razem, gdy zaznaczony jest przycisk **Kliknij mnie.**</span><span class="sxs-lookup"><span data-stu-id="a49cc-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a49cc-155">Licznik w `Counter` komponencie nadal zwiększa się o jeden.</span><span class="sxs-lookup"><span data-stu-id="a49cc-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="a49cc-156">Droga do komponentów</span><span class="sxs-lookup"><span data-stu-id="a49cc-156">Route to components</span></span>

<span data-ttu-id="a49cc-157">Dyrektywa `@page` w górnej części pliku *Counter.brzytwa* określa, że `Counter` składnik jest punktem końcowym routingu.</span><span class="sxs-lookup"><span data-stu-id="a49cc-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="a49cc-158">Składnik `Counter` obsługuje żądania wysyłane `/counter`do .</span><span class="sxs-lookup"><span data-stu-id="a49cc-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="a49cc-159">Bez `@page` dyrektywy składnik nie obsługuje kierowanych żądań, ale składnik może nadal być używany przez inne składniki.</span><span class="sxs-lookup"><span data-stu-id="a49cc-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="a49cc-160">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="a49cc-160">Dependency injection</span></span>

### <a name="opno-locblazor-server-experience"></a>Blazor<span data-ttu-id="a49cc-161">Doświadczenie serwera</span><span class="sxs-lookup"><span data-stu-id="a49cc-161"> Server experience</span></span>

<span data-ttu-id="a49cc-162">W przypadku Blazor pracy z `WeatherForecastService` aplikacją Server usługa jest `Startup.ConfigureServices`rejestrowana jako [singleton](xref:fundamentals/dependency-injection#service-lifetimes) w pliku .</span><span class="sxs-lookup"><span data-stu-id="a49cc-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a49cc-163">Wystąpienie usługi jest dostępne w całej aplikacji za pośrednictwem [iniekcji zależności (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="a49cc-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="a49cc-164">Dyrektywa `@inject` jest używana do wstrzykiwania `WeatherForecastService` wystąpienia `FetchData` usługi do składnika.</span><span class="sxs-lookup"><span data-stu-id="a49cc-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="a49cc-165">*Strony/FetchData.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="a49cc-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="a49cc-166">Składnik `FetchData` używa wtryskiwacza `ForecastService`usługi, jak , `WeatherForecast` aby pobrać tablicę obiektów:</span><span class="sxs-lookup"><span data-stu-id="a49cc-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="opno-locblazor-webassembly-experience"></a>Blazor<span data-ttu-id="a49cc-167">WebAssembly doświadczenie</span><span class="sxs-lookup"><span data-stu-id="a49cc-167"> WebAssembly experience</span></span>

<span data-ttu-id="a49cc-168">Podczas pracy Blazor z aplikacją WebAssembly jest wstrzykiwany w `HttpClient` celu uzyskania danych prognozy pogody z pliku *weather.json* w folderze *wwwroot/sample-data.*</span><span class="sxs-lookup"><span data-stu-id="a49cc-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="a49cc-169">*Strony/FetchData.brzytwa:*</span><span class="sxs-lookup"><span data-stu-id="a49cc-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="a49cc-170">Pętla służy do renderowania [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) każdego wystąpienia prognozy jako wiersza w tabeli danych pogodowych:</span><span class="sxs-lookup"><span data-stu-id="a49cc-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="a49cc-171">Tworzenie listy zadań do wykonania</span><span class="sxs-lookup"><span data-stu-id="a49cc-171">Build a todo list</span></span>

<span data-ttu-id="a49cc-172">Dodaj nowy składnik do aplikacji, który implementuje prostą listę zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a49cc-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="a49cc-173">Dodaj nowy `Todo` składnik Razor do aplikacji w folderze *Strony.*</span><span class="sxs-lookup"><span data-stu-id="a49cc-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="a49cc-174">W programie Visual Studio kliknij prawym przyciskiem myszy folder **Strony** i wybierz polecenie **Dodaj** > **nowy składnik razor\*\*\*\*elementu** > .</span><span class="sxs-lookup"><span data-stu-id="a49cc-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="a49cc-175">Nazwij plik składnika *Todo.brzytwa*.</span><span class="sxs-lookup"><span data-stu-id="a49cc-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="a49cc-176">W innych środowiskach programistyzuj pusty plik do folderu **Strony** o nazwie *Todo.brzytwa*.</span><span class="sxs-lookup"><span data-stu-id="a49cc-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="a49cc-177">Podaj początkowe znaczniki dla składnika:</span><span class="sxs-lookup"><span data-stu-id="a49cc-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="a49cc-178">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="a49cc-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a49cc-179">Składnik `NavMenu` *(Shared/NavMenu.brzytwa)* jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a49cc-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="a49cc-180">Układy są składnikami, które pozwalają uniknąć powielania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a49cc-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="a49cc-181">Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujące znaczniki elementu listy poniżej istniejących elementów listy w pliku *Shared/NavMenu.razor:*</span><span class="sxs-lookup"><span data-stu-id="a49cc-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="a49cc-182">Odbuduj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-182">Rebuild and run the app.</span></span> <span data-ttu-id="a49cc-183">Odwiedź nową stronę Todo, aby potwierdzić, że link do komponentu `Todo` działa.</span><span class="sxs-lookup"><span data-stu-id="a49cc-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="a49cc-184">Dodaj plik *TodoItem.cs* do katalogu głównego projektu, aby pomieścić klasę reprezentującą element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a49cc-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="a49cc-185">Użyj następującego kodu C# dla `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="a49cc-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="a49cc-186">Powrót do `Todo` komponentu (*Pages/Todo.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="a49cc-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="a49cc-187">Dodaj pole dla elementów do `@code` wykonania w bloku.</span><span class="sxs-lookup"><span data-stu-id="a49cc-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="a49cc-188">Składnik `Todo` używa tego pola do zachowania stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="a49cc-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a49cc-189">Dodaj nieuiszczone znaczniki `foreach` listy i pętlę, aby uczynić każdy element todo jako element listy (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="a49cc-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="a49cc-190">Aplikacja wymaga elementów interfejsu użytkownika do dodawania elementów todo do listy.</span><span class="sxs-lookup"><span data-stu-id="a49cc-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a49cc-191">Dodaj wprowadzanie`<input>`tekstu ( )`<button>`i przycisk ( )`<ul>...</ul>`poniżej listy nieuporządkowanych ( ):</span><span class="sxs-lookup"><span data-stu-id="a49cc-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="a49cc-192">Odbuduj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-192">Rebuild and run the app.</span></span> <span data-ttu-id="a49cc-193">Po wybraniu przycisku **Dodaj todo** nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest podłączony do przycisku.</span><span class="sxs-lookup"><span data-stu-id="a49cc-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="a49cc-194">Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją `@onclick` dla wyborów przycisków przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a49cc-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="a49cc-195">Metoda `AddTodo` C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="a49cc-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="a49cc-196">Aby uzyskać tytuł nowego elementu todo, `newTodo` dodaj pole ciągu `@code` w górnej części bloku i powiąż je z wartością wprowadzania tekstu przy użyciu atrybutu `bind` w elemencie: `<input>`</span><span class="sxs-lookup"><span data-stu-id="a49cc-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a49cc-197">Zaktualizuj `AddTodo` `TodoItem` metodę, aby dodać z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="a49cc-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a49cc-198">Wyczyść wartość wprowadzania `newTodo` tekstu, ustawiając pusty ciąg:</span><span class="sxs-lookup"><span data-stu-id="a49cc-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="a49cc-199">Odbuduj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-199">Rebuild and run the app.</span></span> <span data-ttu-id="a49cc-200">Dodaj kilka elementów todo do listy zadań do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="a49cc-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="a49cc-201">Tekst tytułu dla każdego elementu todo można edytować, a pole wyboru może pomóc użytkownikowi śledzić ukończone elementy.</span><span class="sxs-lookup"><span data-stu-id="a49cc-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a49cc-202">Dodaj dane wejściowe pola wyboru dla każdego elementu `IsDone` todo i powiąż jego wartość z właściwością.</span><span class="sxs-lookup"><span data-stu-id="a49cc-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a49cc-203">Zmiana `@todo.Title` elementu `<input>` powiązanego `@todo.Title`z:</span><span class="sxs-lookup"><span data-stu-id="a49cc-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="a49cc-204">Aby sprawdzić, czy te wartości `<h3>` są powiązane, zaktualizuj nagłówek, aby wyświetlić liczbę elementów do wykonania, które nie zostały ukończone (`IsDone` jest `false`).</span><span class="sxs-lookup"><span data-stu-id="a49cc-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a49cc-205">Wypełniony `Todo` komponent (*Pages/Todo.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="a49cc-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="a49cc-206">Odbuduj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="a49cc-206">Rebuild and run the app.</span></span> <span data-ttu-id="a49cc-207">Dodaj elementy todo, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="a49cc-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
