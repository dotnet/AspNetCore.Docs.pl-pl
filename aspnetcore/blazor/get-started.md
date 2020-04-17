---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij od Blazor tworzenia Blazor aplikacji z wybranym oprzyrządowania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488731"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="889a8-103">Zacznij cie z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="889a8-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="889a8-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="889a8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="889a8-105">Aby rozpocząć pracę z Blazorem, postępuj zgodnie ze wskazówkami dotyczącymi wyboru narzędzi:</span><span class="sxs-lookup"><span data-stu-id="889a8-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="889a8-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="889a8-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="889a8-107">Aby utworzyć aplikacje Blazor Server, zainstaluj najnowszą wersję [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z obciążeniem **ASP.NET i tworzenia sieci Web.**</span><span class="sxs-lookup"><span data-stu-id="889a8-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="889a8-108">Aby utworzyć aplikacje Blazor Server i Blazor WebAssembly, zainstaluj najnowszą wersję zapoznawczą [programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) z obciążeniem **ASP.NET i tworzenia stron internetowych.**</span><span class="sxs-lookup"><span data-stu-id="889a8-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="889a8-109">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor WebAssembly* i *Blazor Server*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="889a8-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="889a8-110">Zainstaluj szablon [podglądu Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="889a8-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. <span data-ttu-id="889a8-111">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="889a8-111">Create a new project.</span></span>

1. <span data-ttu-id="889a8-112">Wybierz **aplikację Blazor**.</span><span class="sxs-lookup"><span data-stu-id="889a8-112">Select **Blazor App**.</span></span> <span data-ttu-id="889a8-113">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="889a8-113">Select **Next**.</span></span>

1. <span data-ttu-id="889a8-114">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="889a8-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="889a8-115">Upewnij się, że wpis **Lokalizacja** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="889a8-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="889a8-116">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="889a8-116">Select **Create**.</span></span>

1. <span data-ttu-id="889a8-117">Aby uzyskać środowisko Blazor WebAssembly (Visual Studio 16.6 Preview 2 lub nowsze), wybierz szablon **aplikacji Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="889a8-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="889a8-118">Aby uzyskać środowisko serwera Blazor (Visual Studio 16.4 lub nowsze), wybierz szablon **aplikacji Serwera Blazor.**</span><span class="sxs-lookup"><span data-stu-id="889a8-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="889a8-119">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="889a8-119">Select **Create**.</span></span>

1. <span data-ttu-id="889a8-120">Naciśnij <kbd>klawisz Ctrl</kbd>+<kbd>F5,</kbd> aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="889a8-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="889a8-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="889a8-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="889a8-122">Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="889a8-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="889a8-123">Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="889a8-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="889a8-124">Do korzystania z szablonu 3.2 Preview **4** Blazor WebAssembly wymagany jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej.</span><span class="sxs-lookup"><span data-stu-id="889a8-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="889a8-125">Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="889a8-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="889a8-126">Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="889a8-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="889a8-127">Zainstaluj najnowsze [rozszerzenie kodu programu Visual Studio w języku C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) i `debug.javascript.usePreview` rozszerzenie `true` [debugera JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z ustawieniem .</span><span class="sxs-lookup"><span data-stu-id="889a8-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="889a8-128">Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="889a8-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="889a8-129">Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="889a8-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="889a8-130">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="889a8-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="889a8-131">Otwórz folder *WebApplication1* w programie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="889a8-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="889a8-132">IDE żąda, aby dodać zasoby do tworzenia i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="889a8-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="889a8-133">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="889a8-133">Select **Yes**.</span></span>

1. <span data-ttu-id="889a8-134">Za pomocą serwera Blazor Uruchom aplikację przy użyciu debugera kodu programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="889a8-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="889a8-135">Z Blazor WebAssembly, uruchom aplikację za pomocą **.NET Core Launch (Blazor Standalone)** uruchom konfigurację, a następnie uruchom przeglądarkę za pomocą **.NET Core Debug Blazor Web Assembly w** konfiguracji uruchamiania Chrome (wymaga Chrome).</span><span class="sxs-lookup"><span data-stu-id="889a8-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="889a8-136">Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="889a8-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="889a8-137">W przeglądarce przejdź `https://localhost:5001`do pliku .</span><span class="sxs-lookup"><span data-stu-id="889a8-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="889a8-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="889a8-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="889a8-139">Blazor Server jest obsługiwany w programie Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="889a8-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="889a8-140">Blazor WebAssembly nie jest obecnie obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="889a8-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="889a8-141">Aby utworzyć aplikacje Blazor WebAssembly w systemie macOS, postępuj zgodnie ze wskazówkami na karcie **.NET Core CLI.**</span><span class="sxs-lookup"><span data-stu-id="889a8-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="889a8-142">Zainstaluj [program Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="889a8-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="889a8-143">Wybierz **pozycję Plik** > **nowego rozwiązania** lub utwórz nowy **projekt**.</span><span class="sxs-lookup"><span data-stu-id="889a8-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="889a8-144">Na pasku bocznym wybierz pozycję **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="889a8-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="889a8-145">Wybierz szablon **aplikacji Serwera Blazor.**</span><span class="sxs-lookup"><span data-stu-id="889a8-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="889a8-146">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="889a8-146">Select **Create**.</span></span>

   <span data-ttu-id="889a8-147">Aby uzyskać informacje na temat modelu <xref:blazor/hosting-models>hostingu Serwera Blazor, zobacz .</span><span class="sxs-lookup"><span data-stu-id="889a8-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="889a8-148">Ustaw **platformę docelową** na **.NET Core 3.1** i wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="889a8-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="889a8-149">W polu **Nazwa projektu** nazwij aplikację `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="889a8-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="889a8-150">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="889a8-150">Select **Create**.</span></span>

1. <span data-ttu-id="889a8-151">Wybierz **uruchom** > **uruchom bez debugowania,** aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="889a8-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="889a8-152">Uruchom aplikację za pomocą **debugowania startowego,** aby uruchomić aplikację *za pomocą debugera*.</span><span class="sxs-lookup"><span data-stu-id="889a8-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="889a8-153">Jeśli pojawia się monit, aby ufać certyfikatowi dewelopera, zaufaj certyfikatowi i kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="889a8-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="889a8-154">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="889a8-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="889a8-155">Zainstaluj pakiet [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="889a8-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="889a8-156">Opcjonalnie zainstaluj szablon podglądu [Blazor WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="889a8-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="889a8-157">Do korzystania z szablonu 3.2 Preview **4** Blazor WebAssembly wymagany jest [plik .NET Core SDK w wersji 3.1.201 lub](https://dotnet.microsoft.com/download/dotnet-core/3.1) nowszej.</span><span class="sxs-lookup"><span data-stu-id="889a8-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="889a8-158">Potwierdź zainstalowaną wersję .NET `dotnet --version` Core SDK, uruchamiając go w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="889a8-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="889a8-159">Aby uzyskać środowisko serwera Blazor, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="889a8-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="889a8-160">Aby uzyskać środowisko Blazor WebAssembly, wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="889a8-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="889a8-161">Aby uzyskać informacje na temat dwóch modeli hostingowych Blazor, *Blazor Server* i *Blazor WebAssembly*, zobacz <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="889a8-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="889a8-162">W przeglądarce przejdź `https://localhost:5001`do pliku .</span><span class="sxs-lookup"><span data-stu-id="889a8-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="889a8-163">Wiele stron jest dostępnych na kartach na pasku bocznym:</span><span class="sxs-lookup"><span data-stu-id="889a8-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="889a8-164">Strona główna</span><span class="sxs-lookup"><span data-stu-id="889a8-164">Home</span></span>
* <span data-ttu-id="889a8-165">Licznik</span><span class="sxs-lookup"><span data-stu-id="889a8-165">Counter</span></span>
* <span data-ttu-id="889a8-166">Pobieranie danych</span><span class="sxs-lookup"><span data-stu-id="889a8-166">Fetch data</span></span>

<span data-ttu-id="889a8-167">Na stronie Licznik wybierz przycisk **Kliknij mnie,** aby zwiększać licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="889a8-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="889a8-168">Zwiększanie licznika na stronie sieci Web zwykle wymaga pisania Blazor javascript, ale z można użyć języka C#.</span><span class="sxs-lookup"><span data-stu-id="889a8-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="889a8-169">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="889a8-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="889a8-170">Żądanie `/counter` w przeglądarce, zgodnie z `@page` dyrektywą u góry, powoduje, że `Counter` składnik do renderowania jego zawartości.</span><span class="sxs-lookup"><span data-stu-id="889a8-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="889a8-171">Składniki renderowania w pamięci reprezentacji drzewa renderowania, które następnie mogą służyć do aktualizacji interfejsu użytkownika w sposób elastyczny i wydajny.</span><span class="sxs-lookup"><span data-stu-id="889a8-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="889a8-172">Za każdym razem, gdy wybrany jest przycisk **Kliknij mnie:**</span><span class="sxs-lookup"><span data-stu-id="889a8-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="889a8-173">Zdarzenie `onclick` jest uruchamiane.</span><span class="sxs-lookup"><span data-stu-id="889a8-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="889a8-174">Metoda `IncrementCount` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="889a8-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="889a8-175">Jest `currentCount` przyrostowany.</span><span class="sxs-lookup"><span data-stu-id="889a8-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="889a8-176">Składnik jest renderowany ponownie.</span><span class="sxs-lookup"><span data-stu-id="889a8-176">The component is rendered again.</span></span>

<span data-ttu-id="889a8-177">Środowisko wykonawcze porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do modelu obiektu dokumentu (DOM).</span><span class="sxs-lookup"><span data-stu-id="889a8-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="889a8-178">Dodaj składnik do innego składnika przy użyciu składni HTML.</span><span class="sxs-lookup"><span data-stu-id="889a8-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="889a8-179">Na przykład dodaj `Counter` składnik do strony głównej aplikacji, `<Counter />` dodając `Index` element do składnika.</span><span class="sxs-lookup"><span data-stu-id="889a8-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="889a8-180">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="889a8-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="889a8-181">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="889a8-181">Run the app.</span></span> <span data-ttu-id="889a8-182">Strona główna ma swój własny `Counter` licznik dostarczony przez składnik.</span><span class="sxs-lookup"><span data-stu-id="889a8-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="889a8-183">Parametry komponentu są określane przy użyciu atrybutów lub [zawartości podrzędnej,](xref:blazor/components#child-content)które umożliwiają ustawienie właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="889a8-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="889a8-184">Aby dodać parametr `Counter` do składnika, zaktualizuj blok składnika: `@code`</span><span class="sxs-lookup"><span data-stu-id="889a8-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="889a8-185">Dodaj właściwość `IncrementAmount` publiczną `[Parameter]` dla z atrybutem.</span><span class="sxs-lookup"><span data-stu-id="889a8-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="889a8-186">Zmień `IncrementCount` metodę, aby `IncrementAmount` użyć podczas `currentCount`zwiększania wartości .</span><span class="sxs-lookup"><span data-stu-id="889a8-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="889a8-187">*Strony/Counter.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="889a8-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="889a8-188">Określ `IncrementAmount` element `Index` w `<Counter>` elemencie komponentu za pomocą atrybutu.</span><span class="sxs-lookup"><span data-stu-id="889a8-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="889a8-189">*Strony/Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="889a8-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="889a8-190">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="889a8-190">Run the app.</span></span> <span data-ttu-id="889a8-191">Komponent `Index` ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy zaznaczony jest przycisk **Kliknij mnie.**</span><span class="sxs-lookup"><span data-stu-id="889a8-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="889a8-192">Składnik `Counter` *(Counter.brzytwa)* w `/counter` dalszym ciągu zwiększa się o jeden.</span><span class="sxs-lookup"><span data-stu-id="889a8-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="889a8-193">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="889a8-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="889a8-194">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="889a8-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
