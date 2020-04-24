---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij korzystać z Blazor programu, tworząc Blazor aplikację przy użyciu wybranego przez siebie narzędzia.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111074"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="cb716-103">Wprowadzenie do ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cb716-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="cb716-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb716-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cb716-105">Aby rozpocząć pracę z usługą Blazor, postępuj zgodnie ze wskazówkami dotyczącymi wybranych narzędzi:</span><span class="sxs-lookup"><span data-stu-id="cb716-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb716-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb716-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cb716-107">Aby utworzyć aplikacje serwera Blazor, zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) przy użyciu obciążeń **ASP.NET i Web Development** .</span><span class="sxs-lookup"><span data-stu-id="cb716-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="cb716-108">Aby utworzyć Blazor Server i Blazor aplikacje webassembly, zainstaluj najnowszą wersję zapoznawczą programu [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) przy użyciu obciążenia **ASP.NET i sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="cb716-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="cb716-109">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="cb716-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cb716-110">Zainstaluj szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb716-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="cb716-111">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="cb716-111">Create a new project.</span></span>

1. <span data-ttu-id="cb716-112">Wybierz pozycję **aplikacja Blazor**.</span><span class="sxs-lookup"><span data-stu-id="cb716-112">Select **Blazor App**.</span></span> <span data-ttu-id="cb716-113">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="cb716-113">Select **Next**.</span></span>

1. <span data-ttu-id="cb716-114">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="cb716-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="cb716-115">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="cb716-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="cb716-116">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cb716-116">Select **Create**.</span></span>

1. <span data-ttu-id="cb716-117">W przypadku środowiska webassembly Blazor (Visual Studio 16,6 Preview 2 lub nowszego) wybierz szablon **aplikacji Blazor webassembly** .</span><span class="sxs-lookup"><span data-stu-id="cb716-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="cb716-118">W przypadku środowiska serwera Blazor (Visual Studio 16,4 lub nowszego) wybierz szablon **aplikacji Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="cb716-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="cb716-119">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cb716-119">Select **Create**.</span></span>

1. <span data-ttu-id="cb716-120">Naciśnij klawisz <kbd>Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="cb716-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cb716-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cb716-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="cb716-122">Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cb716-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="cb716-123">Opcjonalnie można zainstalować szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb716-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="cb716-124">[Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było użyć szablonu webassembly 3,2 Blazor.</span><span class="sxs-lookup"><span data-stu-id="cb716-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="cb716-125">Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.</span><span class="sxs-lookup"><span data-stu-id="cb716-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="cb716-126">Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="cb716-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="cb716-127">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym `true`na.</span><span class="sxs-lookup"><span data-stu-id="cb716-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="cb716-128">W przypadku środowiska serwera Blazor wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb716-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="cb716-129">W przypadku środowiska webassembly Blazor wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb716-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="cb716-130">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor Server* i *Blazor webassembly*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="cb716-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cb716-131">Otwórz folder *WebApplication1* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cb716-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="cb716-132">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="cb716-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="cb716-133">Wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="cb716-133">Select **Yes**.</span></span>

1. <span data-ttu-id="cb716-134">Na serwerze Blazor Uruchom aplikację przy użyciu debugera Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cb716-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="cb716-135">W programie Blazor webassembly Uruchom aplikację przy użyciu konfiguracji uruchamiania programu **.NET Core (Blazor Standalone)** , a następnie uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly** (wymaga programu Chrome).</span><span class="sxs-lookup"><span data-stu-id="cb716-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="cb716-136">Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="cb716-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="cb716-137">W przeglądarce przejdź do `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cb716-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cb716-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cb716-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cb716-139">Serwer Blazor jest obsługiwany w Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="cb716-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="cb716-140">Zestaw webassembly Blazor nie jest obecnie obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="cb716-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="cb716-141">Aby kompilować Blazor aplikacje webassembly na macOS, postępuj zgodnie ze wskazówkami na karcie **interfejs wiersza polecenia platformy .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="cb716-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="cb716-142">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="cb716-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="cb716-143">Wybierz pozycję **plik** > **nowe rozwiązanie** lub Utwórz **Nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="cb716-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="cb716-144">Na pasku bocznym wybierz pozycję**aplikacja** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="cb716-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="cb716-145">Wybierz szablon **aplikacji Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="cb716-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="cb716-146">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cb716-146">Select **Create**.</span></span>

   <span data-ttu-id="cb716-147">Aby uzyskać informacje na temat modelu hostingu serwera Blazor, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="cb716-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cb716-148">Ustaw platformę **docelową** na **platformę .NET Core 3,1** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="cb716-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="cb716-149">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="cb716-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="cb716-150">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cb716-150">Select **Create**.</span></span>

1. <span data-ttu-id="cb716-151">Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="cb716-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="cb716-152">Uruchom aplikację przy użyciu **Rozpocznij debugowanie** , aby uruchomić aplikację *za pomocą debugera*.</span><span class="sxs-lookup"><span data-stu-id="cb716-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="cb716-153">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="cb716-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb716-154">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cb716-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="cb716-155">Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="cb716-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="cb716-156">Opcjonalnie można zainstalować szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb716-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="cb716-157">[Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było użyć szablonu webassembly 3,2 Blazor.</span><span class="sxs-lookup"><span data-stu-id="cb716-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="cb716-158">Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.</span><span class="sxs-lookup"><span data-stu-id="cb716-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="cb716-159">W przypadku środowiska serwera Blazor należy wykonać następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb716-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cb716-160">W przypadku środowiska webassembly Blazor wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb716-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="cb716-161">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor Server* i *Blazor webassembly*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="cb716-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="cb716-162">W przeglądarce przejdź do `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="cb716-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="cb716-163">Na pasku bocznym są dostępne wiele stron:</span><span class="sxs-lookup"><span data-stu-id="cb716-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="cb716-164">Strona główna</span><span class="sxs-lookup"><span data-stu-id="cb716-164">Home</span></span>
* <span data-ttu-id="cb716-165">Licznik</span><span class="sxs-lookup"><span data-stu-id="cb716-165">Counter</span></span>
* <span data-ttu-id="cb716-166">Pobieranie danych</span><span class="sxs-lookup"><span data-stu-id="cb716-166">Fetch data</span></span>

<span data-ttu-id="cb716-167">Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="cb716-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="cb716-168">Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript, ale przy Blazor użyciu języka C#.</span><span class="sxs-lookup"><span data-stu-id="cb716-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="cb716-169">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cb716-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="cb716-170">Żądanie `/counter` w przeglądarce, zgodnie z definicją w `@page` dyrektywie u góry, powoduje, że `Counter` składnik renderuje jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="cb716-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="cb716-171">Składniki są renderowane w postaci reprezentacji drzewa renderowania, która może być następnie używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.</span><span class="sxs-lookup"><span data-stu-id="cb716-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="cb716-172">Za każdym razem, gdy zostanie wybrany przycisk **kliknij mnie** :</span><span class="sxs-lookup"><span data-stu-id="cb716-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="cb716-173">`onclick` Zdarzenie jest wyzwalane.</span><span class="sxs-lookup"><span data-stu-id="cb716-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="cb716-174">`IncrementCount` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="cb716-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="cb716-175">Wartość `currentCount` jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="cb716-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="cb716-176">Składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="cb716-176">The component is rendered again.</span></span>

<span data-ttu-id="cb716-177">Środowisko uruchomieniowe porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="cb716-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="cb716-178">Dodaj składnik do innego składnika przy użyciu składni języka HTML.</span><span class="sxs-lookup"><span data-stu-id="cb716-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="cb716-179">Na przykład Dodaj `Counter` składnik do strony głównej aplikacji przez dodanie `<Counter />` elementu do `Index` składnika.</span><span class="sxs-lookup"><span data-stu-id="cb716-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="cb716-180">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cb716-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="cb716-181">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="cb716-181">Run the app.</span></span> <span data-ttu-id="cb716-182">Strona główna ma swój własny licznik dostarczony przez `Counter` składnik.</span><span class="sxs-lookup"><span data-stu-id="cb716-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="cb716-183">Parametry składnika są określone przy użyciu atrybutów lub [zawartości podrzędnej](xref:blazor/components#child-content), które umożliwiają ustawianie właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="cb716-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="cb716-184">Aby dodać parametr do `Counter` składnika, zaktualizuj `@code` blok składnika:</span><span class="sxs-lookup"><span data-stu-id="cb716-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="cb716-185">Dodaj właściwość publiczną dla `IncrementAmount` `[Parameter]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="cb716-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="cb716-186">Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` podczas zwiększania wartości. `currentCount`</span><span class="sxs-lookup"><span data-stu-id="cb716-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="cb716-187">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cb716-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="cb716-188">Określ `<Counter>` element `IncrementAmount` w elemencie `Index` składnika przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="cb716-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="cb716-189">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="cb716-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="cb716-190">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="cb716-190">Run the app.</span></span> <span data-ttu-id="cb716-191">`Index` Składnik ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** .</span><span class="sxs-lookup"><span data-stu-id="cb716-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="cb716-192">Składnik (*Counter. Razor*) w `/counter` dalszym ciągu zwiększa się o jeden. `Counter`</span><span class="sxs-lookup"><span data-stu-id="cb716-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb716-193">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="cb716-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="cb716-194">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cb716-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
