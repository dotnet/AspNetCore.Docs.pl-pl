---
title: Tworzenie pierwszej Blazor aplikacji
author: guardrex
description: Kompiluj Blazor aplikację krok po kroku.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 5a5a56ee646cba21a883df2cf686cb1ccb18d7f9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776607"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="c7534-103">Tworzenie pierwszej Blazor aplikacji</span><span class="sxs-lookup"><span data-stu-id="c7534-103">Build your first Blazor app</span></span>

<span data-ttu-id="c7534-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c7534-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c7534-105">W tym samouczku pokazano, jak utworzyć i zmodyfikować Blazor aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

## <a name="build-components"></a><span data-ttu-id="c7534-106">Składniki kompilacji</span><span class="sxs-lookup"><span data-stu-id="c7534-106">Build components</span></span>

1. <span data-ttu-id="c7534-107">Postępuj zgodnie ze wskazówkami zawartymi <xref:blazor/get-started> w Blazor artykule, aby utworzyć projekt dla tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="c7534-107">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="c7534-108">Nazwij projekt *todolist*.</span><span class="sxs-lookup"><span data-stu-id="c7534-108">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="c7534-109">Przejdź do każdej z trzech stron aplikacji w folderze *Pages* : Home, Counter i Fetch Data.</span><span class="sxs-lookup"><span data-stu-id="c7534-109">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="c7534-110">Te strony są implementowane przez Razor pliki składników *index. Razor*, *Counter. Razor*i *FetchData. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c7534-110">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="c7534-111">Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="c7534-111">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="c7534-112">Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c7534-112">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="c7534-113">W Blazorprogramie można napisać w języku C#.</span><span class="sxs-lookup"><span data-stu-id="c7534-113">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="c7534-114">Sprawdzanie implementacji `Counter` składnika w pliku *Counter. Razor* .</span><span class="sxs-lookup"><span data-stu-id="c7534-114">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="c7534-115">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-115">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="c7534-116">Interfejs użytkownika `Counter` składnika jest definiowany przy użyciu języka HTML.</span><span class="sxs-lookup"><span data-stu-id="c7534-116">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="c7534-117">Logika renderowania dynamicznego (na przykład pętle, warunkowe, wyrażenia) jest dodawana przy użyciu osadzonej [Razor](xref:mvc/views/razor)składni języka C# o nazwie.</span><span class="sxs-lookup"><span data-stu-id="c7534-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c7534-118">Logika renderowania w kodzie HTML i języka C# jest konwertowana na klasę składnika w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="c7534-118">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="c7534-119">Nazwa wygenerowanej klasy .NET jest zgodna z nazwą pliku.</span><span class="sxs-lookup"><span data-stu-id="c7534-119">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="c7534-120">Elementy członkowskie klasy składnika są zdefiniowane w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="c7534-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c7534-121">W `@code` bloku, stan składnika (właściwości, pola) i metody są określone dla obsługi zdarzeń lub do definiowania innej logiki składnika.</span><span class="sxs-lookup"><span data-stu-id="c7534-121">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="c7534-122">Te elementy członkowskie są następnie używane jako część logiki renderowania składnika i dla zdarzeń obsługi.</span><span class="sxs-lookup"><span data-stu-id="c7534-122">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="c7534-123">Po wybraniu przycisku **kliknij do mnie** :</span><span class="sxs-lookup"><span data-stu-id="c7534-123">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="c7534-124">`Counter` Zarejestrowana `onclick` procedura obsługi jest wywoływana ( `IncrementCount` Metoda).</span><span class="sxs-lookup"><span data-stu-id="c7534-124">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="c7534-125">`Counter` Składnik ponownie generuje drzewo renderowania.</span><span class="sxs-lookup"><span data-stu-id="c7534-125">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="c7534-126">Nowe drzewo renderowania jest porównywane z poprzednią.</span><span class="sxs-lookup"><span data-stu-id="c7534-126">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="c7534-127">Stosowane są tylko modyfikacje Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="c7534-127">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="c7534-128">Wyświetlana liczba jest aktualizowana.</span><span class="sxs-lookup"><span data-stu-id="c7534-128">The displayed count is updated.</span></span>

1. <span data-ttu-id="c7534-129">Zmodyfikuj logikę języka C# `Counter` składnika, aby zwiększyć liczbę o dwa zamiast jednego.</span><span class="sxs-lookup"><span data-stu-id="c7534-129">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="c7534-130">Skompiluj ponownie i uruchom aplikację, aby zobaczyć zmiany.</span><span class="sxs-lookup"><span data-stu-id="c7534-130">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="c7534-131">Wybierz przycisk **kliknij mnie** .</span><span class="sxs-lookup"><span data-stu-id="c7534-131">Select the **Click me** button.</span></span> <span data-ttu-id="c7534-132">Licznik jest zwiększany o dwa.</span><span class="sxs-lookup"><span data-stu-id="c7534-132">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="c7534-133">Używanie składników</span><span class="sxs-lookup"><span data-stu-id="c7534-133">Use components</span></span>

<span data-ttu-id="c7534-134">Uwzględnij składnik w innym składniku przy użyciu składni języka HTML.</span><span class="sxs-lookup"><span data-stu-id="c7534-134">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="c7534-135">`Counter` Dodaj składnik do `Index` składnika aplikacji, dodając `<Counter />` element do `Index` składnika (*index. Razor*).</span><span class="sxs-lookup"><span data-stu-id="c7534-135">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="c7534-136">Blazor Jeśli używasz zestawu webassembly do obsługi tego środowiska, `SurveyPrompt` składnik jest używany przez `Index` komponent.</span><span class="sxs-lookup"><span data-stu-id="c7534-136">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="c7534-137">Zastąp `<SurveyPrompt>` element `<Counter />` elementem.</span><span class="sxs-lookup"><span data-stu-id="c7534-137">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="c7534-138">Jeśli używasz aplikacji Blazor serwera do tego środowiska, Dodaj `<Counter />` element do `Index` składnika:</span><span class="sxs-lookup"><span data-stu-id="c7534-138">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="c7534-139">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-139">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="c7534-140">Skompiluj ponownie i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-140">Rebuild and run the app.</span></span> <span data-ttu-id="c7534-141">`Index` Składnik ma swój własny licznik.</span><span class="sxs-lookup"><span data-stu-id="c7534-141">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="c7534-142">Parametry składnika</span><span class="sxs-lookup"><span data-stu-id="c7534-142">Component parameters</span></span>

<span data-ttu-id="c7534-143">Składniki mogą także mieć parametry.</span><span class="sxs-lookup"><span data-stu-id="c7534-143">Components can also have parameters.</span></span> <span data-ttu-id="c7534-144">Parametry składnika są definiowane przy użyciu właściwości publicznych w klasie składnika z `[Parameter]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="c7534-144">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c7534-145">Użyj atrybutów, aby określić argumenty dla składnika w znaczniku.</span><span class="sxs-lookup"><span data-stu-id="c7534-145">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="c7534-146">Zaktualizuj kod `@code` C# składnika w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="c7534-146">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="c7534-147">Dodaj publiczną `IncrementAmount` właściwość z `[Parameter]` atrybutem.</span><span class="sxs-lookup"><span data-stu-id="c7534-147">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="c7534-148">Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` właściwości podczas zwiększania wartości. `currentCount`</span><span class="sxs-lookup"><span data-stu-id="c7534-148">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="c7534-149">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-149">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="c7534-150">Określ `IncrementAmount` parametr w `Index` `<Counter>` elemencie składnika przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c7534-150">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="c7534-151">Ustaw wartość, aby zwiększyć licznik o dziesięć.</span><span class="sxs-lookup"><span data-stu-id="c7534-151">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="c7534-152">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-152">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="c7534-153">Załaduj `Index` ponownie składnik.</span><span class="sxs-lookup"><span data-stu-id="c7534-153">Reload the `Index` component.</span></span> <span data-ttu-id="c7534-154">Licznik jest zwiększany o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** .</span><span class="sxs-lookup"><span data-stu-id="c7534-154">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="c7534-155">Licznik w `Counter` składniku kontynuuje zwiększanie o jeden.</span><span class="sxs-lookup"><span data-stu-id="c7534-155">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="c7534-156">Kierowanie do składników</span><span class="sxs-lookup"><span data-stu-id="c7534-156">Route to components</span></span>

<span data-ttu-id="c7534-157">Dyrektywa w górnej części pliku *Counter. Razor* określa, że `Counter` składnik jest punktem końcowym routingu. `@page`</span><span class="sxs-lookup"><span data-stu-id="c7534-157">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="c7534-158">`Counter` Składnik obsługuje żądania wysyłane do `/counter`.</span><span class="sxs-lookup"><span data-stu-id="c7534-158">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="c7534-159">Bez `@page` dyrektywy składnik nie obsługuje rozesłanych żądań, ale składnik nadal może być używany przez inne składniki.</span><span class="sxs-lookup"><span data-stu-id="c7534-159">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="c7534-160">Wstrzykiwanie zależności</span><span class="sxs-lookup"><span data-stu-id="c7534-160">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="c7534-161">Środowisko serwera</span><span class="sxs-lookup"><span data-stu-id="c7534-161"> Server experience</span></span>

<span data-ttu-id="c7534-162">W `Startup.ConfigureServices`przypadku pracy z Blazor aplikacją serwera `WeatherForecastService` usługa jest rejestrowana jako [Pojedyncza](xref:fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="c7534-162">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c7534-163">Wystąpienie usługi jest dostępne w całej aplikacji za pośrednictwem [iniekcji zależności (di)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="c7534-163">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="c7534-164">`@inject` Dyrektywa służy do wstrzykiwania wystąpienia `WeatherForecastService` usługi do `FetchData` składnika.</span><span class="sxs-lookup"><span data-stu-id="c7534-164">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="c7534-165">*Strony/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-165">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="c7534-166">`FetchData` Składnik używa wstrzykniętej usługi jako `ForecastService`, aby pobrać tablicę `WeatherForecast` obiektów:</span><span class="sxs-lookup"><span data-stu-id="c7534-166">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="c7534-167">Środowisko zestawu webassembly</span><span class="sxs-lookup"><span data-stu-id="c7534-167"> WebAssembly experience</span></span>

<span data-ttu-id="c7534-168">W przypadku pracy z Blazor aplikacją webassembly `HttpClient` wprowadza się w celu uzyskania danych prognozy pogody z pliku *Pogoda. JSON* w folderze *wwwroot/Sample-Data* .</span><span class="sxs-lookup"><span data-stu-id="c7534-168">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="c7534-169">*Strony/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c7534-169">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="c7534-170">[`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) Pętla służy do renderowania każdego wystąpienia prognozy jako wiersza w tabeli danych o pogodzie:</span><span class="sxs-lookup"><span data-stu-id="c7534-170">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="c7534-171">Tworzenie listy zadań do wykonania</span><span class="sxs-lookup"><span data-stu-id="c7534-171">Build a todo list</span></span>

<span data-ttu-id="c7534-172">Dodaj do aplikacji nowy składnik implementujący prostą listę zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c7534-172">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="c7534-173">Dodaj nowy `Todo` Razor składnik do aplikacji w folderze *strony* .</span><span class="sxs-lookup"><span data-stu-id="c7534-173">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="c7534-174">W programie Visual Studio kliknij prawym przyciskiem myszy folder **strony** i wybierz polecenie **Dodaj** > **nowy element**  >  \*\* Razor .\*\*</span><span class="sxs-lookup"><span data-stu-id="c7534-174">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="c7534-175">Nadaj nazwę plikowi do *zrobienia. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c7534-175">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="c7534-176">W innych środowiskach programistycznych Dodaj pusty plik do folderu **Pages** o nazwie do *zrobienia. Razor*.</span><span class="sxs-lookup"><span data-stu-id="c7534-176">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="c7534-177">Podaj początkowe znaczniki dla składnika:</span><span class="sxs-lookup"><span data-stu-id="c7534-177">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="c7534-178">Dodaj `Todo` składnik do paska nawigacyjnego.</span><span class="sxs-lookup"><span data-stu-id="c7534-178">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="c7534-179">`NavMenu` Składnik (*Shared/NavMenu. Razor*) jest używany w układzie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c7534-179">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="c7534-180">Układy są składnikami, które umożliwiają uniknięcie duplikowania zawartości w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c7534-180">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="c7534-181">Dodaj `<NavLink>` element dla `Todo` składnika, dodając następujący znacznik elementu listy poniżej istniejących elementów listy w pliku *Shared/NavMenu. Razor* :</span><span class="sxs-lookup"><span data-stu-id="c7534-181">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="c7534-182">Skompiluj ponownie i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-182">Rebuild and run the app.</span></span> <span data-ttu-id="c7534-183">Odwiedź stronę nowych zadań do wykonania, aby upewnić się, `Todo` że łącze do składnika działa.</span><span class="sxs-lookup"><span data-stu-id="c7534-183">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="c7534-184">Dodaj plik *TodoItem.cs* do katalogu głównego projektu, aby pomieścić klasę reprezentującą element do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c7534-184">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="c7534-185">Użyj następującego kodu w języku C# dla `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="c7534-185">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="c7534-186">Wróć do `Todo` składnika (*strony/do zrobienia. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c7534-186">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="c7534-187">Dodaj pole do zadań do wykonania w `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="c7534-187">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="c7534-188">`Todo` Składnik używa tego pola do obsługi stanu listy zadań do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c7534-188">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="c7534-189">Dodaj znaczniki listy nieuporządkowane i `foreach` pętlę, aby renderować każdy element do wykonania jako element listy`<li>`().</span><span class="sxs-lookup"><span data-stu-id="c7534-189">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="c7534-190">Aplikacja wymaga elementów interfejsu użytkownika do dodawania do listy elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="c7534-190">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="c7534-191">Dodaj tekst wejściowy (`<input>`) i przycisk (`<button>`) poniżej listy nieuporządkowanej (`<ul>...</ul>`):</span><span class="sxs-lookup"><span data-stu-id="c7534-191">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="c7534-192">Skompiluj ponownie i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-192">Rebuild and run the app.</span></span> <span data-ttu-id="c7534-193">Po wybraniu przycisku **Dodaj do zrobienia** nic się nie dzieje, ponieważ program obsługi zdarzeń nie jest w trybie przewodowym.</span><span class="sxs-lookup"><span data-stu-id="c7534-193">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="c7534-194">Dodaj `AddTodo` metodę do `Todo` składnika i zarejestruj ją w celu wybrania opcji przy użyciu `@onclick` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="c7534-194">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="c7534-195">Metoda `AddTodo` C# jest wywoływana, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="c7534-195">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="c7534-196">Aby uzyskać tytuł nowego elementu do wykonania, `newTodo` należy dodać pole ciągu u góry `@code` bloku i powiązać je z wartością tekstu wejściowego przy użyciu `bind` atrybutu w `<input>` elemencie:</span><span class="sxs-lookup"><span data-stu-id="c7534-196">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="c7534-197">Zaktualizuj `AddTodo` metodę, aby dodać `TodoItem` z określonym tytułem do listy.</span><span class="sxs-lookup"><span data-stu-id="c7534-197">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="c7534-198">Wyczyść wartość pola wprowadzanie tekstu przez ustawienie `newTodo` do pustego ciągu:</span><span class="sxs-lookup"><span data-stu-id="c7534-198">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="c7534-199">Skompiluj ponownie i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-199">Rebuild and run the app.</span></span> <span data-ttu-id="c7534-200">Dodaj do listy zadań do zrobienia elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="c7534-200">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="c7534-201">Tekst tytułu dla każdego elementu do wykonania można edytować, a pole wyboru może pomóc użytkownikowi śledzić elementy ukończone.</span><span class="sxs-lookup"><span data-stu-id="c7534-201">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="c7534-202">Dodaj pole wyboru dla każdego elementu do wykonania i powiąż jego wartość z `IsDone` właściwością.</span><span class="sxs-lookup"><span data-stu-id="c7534-202">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="c7534-203">Zmień `@todo.Title` na `<input>` element powiązany z `@todo.Title`:</span><span class="sxs-lookup"><span data-stu-id="c7534-203">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="c7534-204">Aby sprawdzić, czy te wartości są powiązane, zaktualizuj `<h3>` nagłówek, aby pokazać liczbę elementów do wykonania, które nie zostały zakończone (`IsDone` is `false`).</span><span class="sxs-lookup"><span data-stu-id="c7534-204">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="c7534-205">Ukończony `Todo` składnik (*strony/do zrobienia. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c7534-205">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="c7534-206">Skompiluj ponownie i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="c7534-206">Rebuild and run the app.</span></span> <span data-ttu-id="c7534-207">Dodaj elementy do wykonania, aby przetestować nowy kod.</span><span class="sxs-lookup"><span data-stu-id="c7534-207">Add todo items to test the new code.</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
