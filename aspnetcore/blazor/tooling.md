---
title: Narzędzia dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się więcej o narzędziach dostępnych do kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
ms.openlocfilehash: 3ff610225c798624b7ead7d365924ae3d193829d
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944965"
---
<!-- zone_pivot_groups: operating-systems -->

# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="a1fc9-103">Narzędzia dla ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="a1fc9-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="a1fc9-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a1fc9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a1fc9-105">Wybierz Narzędzia dla swojej platformy:</span><span class="sxs-lookup"><span data-stu-id="a1fc9-105">Select the tooling for your platform:</span></span>

* <span data-ttu-id="a1fc9-106">Windows: Wybierz kartę **programu Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-106">Windows: Select the **Visual Studio** tab.</span></span>
* <span data-ttu-id="a1fc9-107">Linux: Wybierz kartę **Visual Studio Code** .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-107">Linux: Select the **Visual Studio Code** tab.</span></span>
* <span data-ttu-id="a1fc9-108">macOS: Wybierz kartę **Visual Studio dla komputerów Mac** .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-108">macOS: Select the **Visual Studio for Mac** tab.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a1fc9-109">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1fc9-109">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a1fc9-110">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-110">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="a1fc9-111">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-111">Create a new project.</span></span>

1. <span data-ttu-id="a1fc9-112">Wybierz pozycję \*\* Blazor aplikacja\*\*.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-112">Select **Blazor App**.</span></span> <span data-ttu-id="a1fc9-113">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-113">Select **Next**.</span></span>

1. <span data-ttu-id="a1fc9-114">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a1fc9-115">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a1fc9-116">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-116">Select **Create**.</span></span>

1. <span data-ttu-id="a1fc9-117">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-117">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a1fc9-118">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-118">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a1fc9-119">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-119">Select **Create**.</span></span>

   <span data-ttu-id="a1fc9-120">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-120">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a1fc9-121">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-121">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a1fc9-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a1fc9-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a1fc9-123">Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="a1fc9-123">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="a1fc9-124">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="a1fc9-124">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="a1fc9-125">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="a1fc9-125">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="a1fc9-126">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-126">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="a1fc9-127">Aby ustawić `debug.javascript.usePreview` `true` przy użyciu interfejsu użytkownika vs Code, Otwórz **File**  >  **Preferences**  >  **Ustawienia** preferencji plików i Wyszukaj `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-127">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="a1fc9-128">Zaznacz pole wyboru **Użyj nowego debugera JavaScript w wersji zapoznawczej dla Node.js i programu Chrome**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-128">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="a1fc9-129">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="a1fc9-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="a1fc9-130">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="a1fc9-130">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a1fc9-131">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-131">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a1fc9-132">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-132">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a1fc9-133">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-133">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a1fc9-134">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-134">Select **Yes**.</span></span>

1. <span data-ttu-id="a1fc9-135">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-135">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a1fc9-136">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="a1fc9-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a1fc9-137">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="a1fc9-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a1fc9-138">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a1fc9-139">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="a1fc9-140">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a1fc9-141">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a1fc9-142">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-142">Select **Next**.</span></span>

   <span data-ttu-id="a1fc9-143">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-143">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a1fc9-144">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="a1fc9-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="a1fc9-145">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="a1fc9-146">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-146">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="a1fc9-147">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-147">Select **Next**.</span></span>

1. <span data-ttu-id="a1fc9-148">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="a1fc9-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a1fc9-149">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-149">Select **Create**.</span></span>

1. <span data-ttu-id="a1fc9-150">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a1fc9-151">Uruchom **aplikację z**  >  przyciskiem Uruchom**debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="a1fc9-152">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a1fc9-153">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="a1fc9-153">The user and keychain passwords are required to trust the certificate.</span></span>

---

<!--

::: zone pivot="os-windows"

1. Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.

1. Create a new project.

1. Select **Blazor App**. Select **Next**.

1. Provide a project name in the **Project name** field or accept the default project name. Confirm the **Location** entry is correct or provide a location for the project. Select **Create**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Create**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-linux"

1. Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:

   ```dotnetcli
   dotnet --version
   ```

1. Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).

1. Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.

   To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`. Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.

1. For a Blazor WebAssembly experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   For a Blazor Server experience, execute the following command in a command shell:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Open the `WebApplication1` folder in Visual Studio Code.

1. The IDE requests that you add assets to build and debug the project. Select **Yes**.

1. Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.

::: zone-end

::: zone pivot="os-macos"

1. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

   For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

If a prompt appears to trust the development certificate, trust the certificate and continue. The user and keychain passwords are required to trust the certificate.

::: zone-end

-->
