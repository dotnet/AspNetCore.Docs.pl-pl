---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij od Blazor tworzenia Blazor aplikacji z wybranym oprzyrządowania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: e9e6eeeb1d29aa529e43d75f5d3951d2c4384d7e
ms.sourcegitcommit: 4506a8f71ece921010ad6b7edebc8b200618f40d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81002915"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="5e06d-103">Zacznij cie z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5e06d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="5e06d-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5e06d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5e06d-105">Aby rozpocząć pracę z Blazorem, postępuj zgodnie ze wskazówkami dotyczącymi wyboru narzędzi:</span><span class="sxs-lookup"><span data-stu-id="5e06d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e06d-106">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e06d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5e06d-107">Aby utworzyć aplikacje Blazor Server, zainstaluj najnowszą wersję [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z obciążeniem **ASP.NET i tworzenia sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="5e06d-108">Aby utworzyć aplikacje Blazor Server i Blazor WebAssembly, zainstaluj najnowszą wersję zapoznawczą [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) z obciążeniem **ASP.NET i tworzenia stron internetowych.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="5e06d-109">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor WebAssembly* i *Blazor Server*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5e06d-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5e06d-110">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="5e06d-110">Create a new project.</span></span>

1. <span data-ttu-id="5e06d-111">Wybierz **aplikację Blazor**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-111">Select **Blazor App**.</span></span> <span data-ttu-id="5e06d-112">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-112">Select **Next**.</span></span>

1. <span data-ttu-id="5e06d-113">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="5e06d-113">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="5e06d-114">Upewnij się, że wpis **Lokalizacja** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="5e06d-114">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5e06d-115">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-115">Select **Create**.</span></span>

1. <span data-ttu-id="5e06d-116">Aby uzyskać środowisko Blazor WebAssembly (Visual Studio 16.6 Preview 2 lub nowsze), wybierz szablon **aplikacji Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-116">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5e06d-117">Aby uzyskać środowisko serwera Blazor (Visual Studio 16.4 lub nowsze), wybierz szablon **aplikacji Serwera Blazor.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-117">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5e06d-118">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-118">Select **Create**.</span></span>

1. <span data-ttu-id="5e06d-119">Naciśnij <kbd>klawisz Ctrl</kbd>+<kbd>F5,</kbd> aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5e06d-119">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e06d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e06d-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5e06d-121">Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="5e06d-121">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="5e06d-122">Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5e06d-122">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="5e06d-123">Do korzystania z szablonu 3.2 Preview 3 Blazor WebAssembly **wymagany** jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej.</span><span class="sxs-lookup"><span data-stu-id="5e06d-123">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="5e06d-124">Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="5e06d-124">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="5e06d-125">Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="5e06d-125">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="5e06d-126">Zainstaluj najnowsze [rozszerzenie kodu programu Visual Studio w języku C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i `debug.javascript.usePreview` rozszerzenie `true` [debugera JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z ustawieniem .</span><span class="sxs-lookup"><span data-stu-id="5e06d-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="5e06d-127">Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="5e06d-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="5e06d-128">Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="5e06d-128">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="5e06d-129">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5e06d-129">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5e06d-130">Otwórz folder *WebApplication1* w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5e06d-130">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="5e06d-131">IDE żąda, aby dodać zasoby do tworzenia i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="5e06d-131">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="5e06d-132">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-132">Select **Yes**.</span></span>

1. <span data-ttu-id="5e06d-133">Za pomocą serwera Blazor Uruchom aplikację przy użyciu debugera kodu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5e06d-133">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="5e06d-134">Z Blazor WebAssembly, uruchom aplikację za pomocą **.NET Core Launch (Blazor Standalone)** uruchom konfigurację, a następnie uruchom przeglądarkę za pomocą **.NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania Chrome (wymaga Chrome).</span><span class="sxs-lookup"><span data-stu-id="5e06d-134">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="5e06d-135">Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="5e06d-135">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="5e06d-136">W przeglądarce przejdź `https://localhost:5001`do pliku .</span><span class="sxs-lookup"><span data-stu-id="5e06d-136">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e06d-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="5e06d-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e06d-138">Blazor Server jest obsługiwany w programie Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="5e06d-138">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="5e06d-139">Blazor WebAssembly nie jest obecnie obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="5e06d-139">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="5e06d-140">Aby utworzyć aplikacje Blazor WebAssembly w systemie macOS, postępuj zgodnie ze wskazówkami na karcie **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-140">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="5e06d-141">Zainstaluj [program Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="5e06d-141">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="5e06d-142">Wybierz **pozycję Plik** > **nowego rozwiązania** lub utwórz nowy **projekt**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-142">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="5e06d-143">Na pasku bocznym wybierz pozycję **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-143">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="5e06d-144">Wybierz szablon **aplikacji Serwera Blazor.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-144">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="5e06d-145">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-145">Select **Create**.</span></span>

   <span data-ttu-id="5e06d-146">Aby uzyskać informacje na temat modelu <xref:blazor/hosting-models>hostingu Serwera Blazor, zobacz .</span><span class="sxs-lookup"><span data-stu-id="5e06d-146">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5e06d-147">Ustaw **platformę docelową** na **.NET Core 3.1** i wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-147">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="5e06d-148">W polu **Nazwa projektu** nazwij aplikację `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="5e06d-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="5e06d-149">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="5e06d-149">Select **Create**.</span></span>

1. <span data-ttu-id="5e06d-150">Wybierz **uruchom** > **uruchom bez debugowania,** aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="5e06d-150">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="5e06d-151">Uruchom aplikację za pomocą **debugowania startowego,** aby uruchomić aplikację *za pomocą debugera*.</span><span class="sxs-lookup"><span data-stu-id="5e06d-151">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="5e06d-152">Jeśli pojawia się monit, aby ufać certyfikatowi dewelopera, zaufaj certyfikatowi i kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="5e06d-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5e06d-153">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="5e06d-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="5e06d-154">Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="5e06d-154">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="5e06d-155">Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="5e06d-155">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > <span data-ttu-id="5e06d-156">Do korzystania z szablonu 3.2 Preview 3 Blazor WebAssembly **wymagany** jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej.</span><span class="sxs-lookup"><span data-stu-id="5e06d-156">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 3 Blazor WebAssembly template.</span></span> <span data-ttu-id="5e06d-157">Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="5e06d-157">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="5e06d-158">Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="5e06d-158">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5e06d-159">Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="5e06d-159">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5e06d-160">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5e06d-160">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="5e06d-161">W przeglądarce przejdź `https://localhost:5001`do pliku .</span><span class="sxs-lookup"><span data-stu-id="5e06d-161">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="5e06d-162">Wiele stron jest dostępnych na kartach na pasku bocznym:</span><span class="sxs-lookup"><span data-stu-id="5e06d-162">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="5e06d-163">Strona główna</span><span class="sxs-lookup"><span data-stu-id="5e06d-163">Home</span></span>
* <span data-ttu-id="5e06d-164">Licznik</span><span class="sxs-lookup"><span data-stu-id="5e06d-164">Counter</span></span>
* <span data-ttu-id="5e06d-165">Pobieranie danych</span><span class="sxs-lookup"><span data-stu-id="5e06d-165">Fetch data</span></span>

<span data-ttu-id="5e06d-166">Na stronie Licznik wybierz przycisk **Kliknij mnie,** aby zwiększać licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="5e06d-166">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5e06d-167">Zwiększanie licznika na stronie sieci Web zwykle wymaga pisania Blazor javascript, ale z można użyć języka C#.</span><span class="sxs-lookup"><span data-stu-id="5e06d-167">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="5e06d-168">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="5e06d-168">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="5e06d-169">Żądanie `/counter` w przeglądarce, zgodnie z `@page` dyrektywą u góry, powoduje, że `Counter` składnik do renderowania jego zawartości.</span><span class="sxs-lookup"><span data-stu-id="5e06d-169">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="5e06d-170">Składniki renderowania w pamięci reprezentacji drzewa renderowania, które następnie mogą służyć do aktualizacji interfejsu użytkownika w sposób elastyczny i wydajny.</span><span class="sxs-lookup"><span data-stu-id="5e06d-170">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="5e06d-171">Za każdym razem, gdy wybrany jest przycisk **Kliknij mnie:**</span><span class="sxs-lookup"><span data-stu-id="5e06d-171">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="5e06d-172">Zdarzenie `onclick` jest uruchamiane.</span><span class="sxs-lookup"><span data-stu-id="5e06d-172">The `onclick` event is fired.</span></span>
* <span data-ttu-id="5e06d-173">Metoda `IncrementCount` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="5e06d-173">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="5e06d-174">Jest `currentCount` przyrostowany.</span><span class="sxs-lookup"><span data-stu-id="5e06d-174">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="5e06d-175">Składnik jest renderowany ponownie.</span><span class="sxs-lookup"><span data-stu-id="5e06d-175">The component is rendered again.</span></span>

<span data-ttu-id="5e06d-176">Środowisko wykonawcze porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do modelu obiektu dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="5e06d-176">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="5e06d-177">Dodaj składnik do innego składnika przy użyciu składni HTML.</span><span class="sxs-lookup"><span data-stu-id="5e06d-177">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="5e06d-178">Na przykład dodaj `Counter` składnik do strony głównej aplikacji, `<Counter />` dodając `Index` element do składnika.</span><span class="sxs-lookup"><span data-stu-id="5e06d-178">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="5e06d-179">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="5e06d-179">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="5e06d-180">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5e06d-180">Run the app.</span></span> <span data-ttu-id="5e06d-181">Strona główna ma swój własny `Counter` licznik dostarczony przez składnik.</span><span class="sxs-lookup"><span data-stu-id="5e06d-181">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="5e06d-182">Parametry komponentu są określane przy użyciu atrybutów lub [zawartości podrzędnej,](xref:blazor/components#child-content)które umożliwiają ustawienie właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="5e06d-182">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="5e06d-183">Aby dodać parametr `Counter` do składnika, zaktualizuj blok składnika: `@code`</span><span class="sxs-lookup"><span data-stu-id="5e06d-183">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="5e06d-184">Dodaj właściwość `IncrementAmount` publiczną `[Parameter]` dla z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="5e06d-184">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="5e06d-185">Zmień `IncrementCount` metodę, aby `IncrementAmount` użyć podczas `currentCount`zwiększania wartości .</span><span class="sxs-lookup"><span data-stu-id="5e06d-185">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="5e06d-186">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="5e06d-186">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="5e06d-187">Określ `IncrementAmount` element `Index` w `<Counter>` elemencie komponentu za pomocą atrybutu.</span><span class="sxs-lookup"><span data-stu-id="5e06d-187">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="5e06d-188">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="5e06d-188">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="5e06d-189">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="5e06d-189">Run the app.</span></span> <span data-ttu-id="5e06d-190">Komponent `Index` ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy zaznaczony jest przycisk **Kliknij mnie.**</span><span class="sxs-lookup"><span data-stu-id="5e06d-190">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5e06d-191">Składnik `Counter` *(Counter.brzytwa)* w `/counter` dalszym ciągu zwiększa się o jeden.</span><span class="sxs-lookup"><span data-stu-id="5e06d-191">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5e06d-192">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="5e06d-192">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="5e06d-193">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5e06d-193">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
