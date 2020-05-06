---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zacznij korzystać z Blazor programu, tworząc Blazor aplikację przy użyciu wybranego przez siebie narzędzia.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769458"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="ec677-103">Wprowadzenie do ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ec677-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="ec677-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ec677-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ec677-105">Aby rozpocząć pracę z usługą Blazor, postępuj zgodnie ze wskazówkami dotyczącymi wybranych narzędzi:</span><span class="sxs-lookup"><span data-stu-id="ec677-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec677-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec677-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ec677-107">Aby utworzyć aplikacje serwera Blazor, zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) przy użyciu obciążeń **ASP.NET i Web Development** .</span><span class="sxs-lookup"><span data-stu-id="ec677-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ec677-108">Aby utworzyć Blazor Server i Blazor aplikacje webassembly, zainstaluj najnowszą wersję zapoznawczą programu [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) przy użyciu obciążenia **ASP.NET i sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="ec677-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ec677-109">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="ec677-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ec677-110">Zainstaluj szablon Blazor webassembly Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ec677-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="ec677-111">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="ec677-111">Create a new project.</span></span>

1. <span data-ttu-id="ec677-112">Wybierz pozycję **aplikacja Blazor**.</span><span class="sxs-lookup"><span data-stu-id="ec677-112">Select **Blazor App**.</span></span> <span data-ttu-id="ec677-113">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ec677-113">Select **Next**.</span></span>

1. <span data-ttu-id="ec677-114">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="ec677-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ec677-115">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="ec677-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ec677-116">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ec677-116">Select **Create**.</span></span>

1. <span data-ttu-id="ec677-117">W przypadku środowiska webassembly Blazor (Visual Studio 16,6 Preview 2 lub nowszego) wybierz szablon **aplikacji Blazor webassembly** .</span><span class="sxs-lookup"><span data-stu-id="ec677-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ec677-118">W przypadku środowiska serwera Blazor (Visual Studio 16,4 lub nowszego) wybierz szablon **aplikacji Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="ec677-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ec677-119">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ec677-119">Select **Create**.</span></span>

1. <span data-ttu-id="ec677-120">Naciśnij klawisz <kbd>Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="ec677-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec677-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec677-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ec677-122">Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ec677-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="ec677-123">Opcjonalnie można zainstalować szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ec677-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="ec677-124">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="ec677-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ec677-125">[Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było 3,2 użyć szablonu webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="ec677-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="ec677-126">Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.</span><span class="sxs-lookup"><span data-stu-id="ec677-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="ec677-127">Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="ec677-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="ec677-128">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym `true`na.</span><span class="sxs-lookup"><span data-stu-id="ec677-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="ec677-129">W przypadku środowiska serwera Blazor wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ec677-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="ec677-130">W przypadku środowiska webassembly Blazor wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ec677-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="ec677-131">Otwórz folder *WebApplication1* w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ec677-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="ec677-132">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="ec677-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ec677-133">Wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="ec677-133">Select **Yes**.</span></span>

1. <span data-ttu-id="ec677-134">Na serwerze Blazor Uruchom aplikację przy użyciu debugera Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ec677-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="ec677-135">W programie Blazor webassembly Uruchom aplikację przy użyciu konfiguracji uruchamiania programu **.NET Core (Blazor Standalone)** , a następnie uruchom przeglądarkę przy użyciu **zestawu .NET Core Debug Blazor Web Assembly** (wymaga programu Chrome).</span><span class="sxs-lookup"><span data-stu-id="ec677-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="ec677-136">Aby uzyskać więcej informacji, zobacz <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="ec677-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="ec677-137">W przeglądarce przejdź do `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ec677-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec677-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ec677-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ec677-139">Serwer Blazor jest obsługiwany w Visual Studio dla komputerów Mac.</span><span class="sxs-lookup"><span data-stu-id="ec677-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="ec677-140">Zestaw webassembly Blazor nie jest obecnie obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="ec677-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="ec677-141">Aby utworzyć aplikacje Blazor webassembly na macOS, postępuj zgodnie ze wskazówkami na karcie **interfejs wiersza polecenia platformy .NET Core** . Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="ec677-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ec677-142">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="ec677-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="ec677-143">Wybierz pozycję **plik** > **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="ec677-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ec677-144">Na pasku bocznym wybierz pozycję**aplikacja** **.NET Core** > .</span><span class="sxs-lookup"><span data-stu-id="ec677-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="ec677-145">Wybierz szablon **aplikacji Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="ec677-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="ec677-146">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ec677-146">Select **Next**.</span></span>

1. <span data-ttu-id="ec677-147">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="ec677-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="ec677-148">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="ec677-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="ec677-149">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="ec677-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="ec677-150">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ec677-150">Select **Next**.</span></span>

1. <span data-ttu-id="ec677-151">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="ec677-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="ec677-152">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ec677-152">Select **Create**.</span></span>

1. <span data-ttu-id="ec677-153">Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="ec677-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="ec677-154">Debugowanie nie jest obecnie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="ec677-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="ec677-155">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="ec677-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ec677-156">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="ec677-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec677-157">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ec677-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ec677-158">Zainstaluj [zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ec677-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="ec677-159">Opcjonalnie można zainstalować szablon Blazor webassembly Preview, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ec677-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="ec677-160">Aby uzyskać informacje na temat dwóch modeli hostingu Blazor, *Blazor webassembly* i *Blazor Server*, <xref:blazor/hosting-models>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="ec677-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ec677-161">[Zestaw .NET Core SDK w wersji 3.1.201 lub nowszej](https://dotnet.microsoft.com/download/dotnet-core/3.1) jest **wymagana** , aby można było 3,2 użyć szablonu webassembly Blazor.</span><span class="sxs-lookup"><span data-stu-id="ec677-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="ec677-162">Potwierdź zainstalowaną zestaw .NET Core SDK wersję, `dotnet --version` uruchamiając polecenie w powłoce poleceń programu.</span><span class="sxs-lookup"><span data-stu-id="ec677-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="ec677-163">W przypadku środowiska serwera Blazor należy wykonać następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ec677-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ec677-164">W przypadku środowiska webassembly Blazor wykonaj następujące polecenia w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="ec677-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="ec677-165">W przeglądarce przejdź do `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ec677-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="ec677-166">Na pasku bocznym są dostępne wiele stron:</span><span class="sxs-lookup"><span data-stu-id="ec677-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="ec677-167">Domowy</span><span class="sxs-lookup"><span data-stu-id="ec677-167">Home</span></span>
* <span data-ttu-id="ec677-168">Licznik</span><span class="sxs-lookup"><span data-stu-id="ec677-168">Counter</span></span>
* <span data-ttu-id="ec677-169">Pobieranie danych</span><span class="sxs-lookup"><span data-stu-id="ec677-169">Fetch data</span></span>

<span data-ttu-id="ec677-170">Na stronie licznik wybierz przycisk **kliknij** , aby zwiększyć licznik bez odświeżania strony.</span><span class="sxs-lookup"><span data-stu-id="ec677-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ec677-171">Zwiększenie licznika na stronie sieci Web zwykle wymaga pisania kodu JavaScript, ale przy Blazor użyciu języka C#.</span><span class="sxs-lookup"><span data-stu-id="ec677-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="ec677-172">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ec677-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="ec677-173">Żądanie `/counter` w przeglądarce, zgodnie z definicją w `@page` dyrektywie u góry, powoduje, że `Counter` składnik renderuje jego zawartość.</span><span class="sxs-lookup"><span data-stu-id="ec677-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="ec677-174">Składniki są renderowane w postaci reprezentacji drzewa renderowania, która może być następnie używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.</span><span class="sxs-lookup"><span data-stu-id="ec677-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="ec677-175">Za każdym razem, gdy zostanie wybrany przycisk **kliknij mnie** :</span><span class="sxs-lookup"><span data-stu-id="ec677-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="ec677-176">`onclick` Zdarzenie jest wyzwalane.</span><span class="sxs-lookup"><span data-stu-id="ec677-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="ec677-177">`IncrementCount` Metoda jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="ec677-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="ec677-178">Wartość `currentCount` jest zwiększana.</span><span class="sxs-lookup"><span data-stu-id="ec677-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="ec677-179">Składnik jest ponownie renderowany.</span><span class="sxs-lookup"><span data-stu-id="ec677-179">The component is rendered again.</span></span>

<span data-ttu-id="ec677-180">Środowisko uruchomieniowe porównuje nową zawartość z poprzednią zawartością i stosuje tylko zmienioną zawartość do Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="ec677-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="ec677-181">Dodaj składnik do innego składnika przy użyciu składni języka HTML.</span><span class="sxs-lookup"><span data-stu-id="ec677-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="ec677-182">Na przykład Dodaj `Counter` składnik do strony głównej aplikacji przez dodanie `<Counter />` elementu do `Index` składnika.</span><span class="sxs-lookup"><span data-stu-id="ec677-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="ec677-183">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ec677-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="ec677-184">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="ec677-184">Run the app.</span></span> <span data-ttu-id="ec677-185">Strona główna ma swój własny licznik dostarczony przez `Counter` składnik.</span><span class="sxs-lookup"><span data-stu-id="ec677-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="ec677-186">Parametry składnika są określone przy użyciu atrybutów lub [zawartości podrzędnej](xref:blazor/components#child-content), które umożliwiają ustawianie właściwości składnika podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="ec677-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="ec677-187">Aby dodać parametr do `Counter` składnika, zaktualizuj `@code` blok składnika:</span><span class="sxs-lookup"><span data-stu-id="ec677-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="ec677-188">Dodaj właściwość publiczną dla `IncrementAmount` `[Parameter]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="ec677-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="ec677-189">Zmień `IncrementCount` metodę, aby użyć `IncrementAmount` podczas zwiększania wartości. `currentCount`</span><span class="sxs-lookup"><span data-stu-id="ec677-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="ec677-190">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ec677-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="ec677-191">Określ `<Counter>` element `IncrementAmount` w elemencie `Index` składnika przy użyciu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="ec677-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="ec677-192">*Pages/index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="ec677-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="ec677-193">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="ec677-193">Run the app.</span></span> <span data-ttu-id="ec677-194">`Index` Składnik ma swój własny licznik, który zwiększa się o dziesięć za każdym razem, gdy jest zaznaczony przycisk **kliknij mnie** .</span><span class="sxs-lookup"><span data-stu-id="ec677-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ec677-195">Składnik (*Counter. Razor*) w `/counter` dalszym ciągu zwiększa się o jeden. `Counter`</span><span class="sxs-lookup"><span data-stu-id="ec677-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec677-196">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="ec677-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="ec677-197">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ec677-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
