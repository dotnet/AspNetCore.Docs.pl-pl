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
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946813"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="2a884-103">Narzędzia dla ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="2a884-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="2a884-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a884-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="2a884-105">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="2a884-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="2a884-106">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="2a884-106">Create a new project.</span></span>

1. <span data-ttu-id="2a884-107">Wybierz pozycję \*\* Blazor aplikacja\*\*.</span><span class="sxs-lookup"><span data-stu-id="2a884-107">Select **Blazor App**.</span></span> <span data-ttu-id="2a884-108">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="2a884-108">Select **Next**.</span></span>

1. <span data-ttu-id="2a884-109">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="2a884-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2a884-110">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="2a884-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2a884-111">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2a884-111">Select **Create**.</span></span>

1. <span data-ttu-id="2a884-112">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a884-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2a884-113">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a884-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2a884-114">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2a884-114">Select **Create**.</span></span>

   <span data-ttu-id="2a884-115">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="2a884-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2a884-116">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a884-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="2a884-117">Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="2a884-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="2a884-118">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2a884-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="2a884-119">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="2a884-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="2a884-120">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) oraz rozszerzenie [JavaScript Debugger (nocne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) z `debug.javascript.usePreview` ustawionym na `true` .</span><span class="sxs-lookup"><span data-stu-id="2a884-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="2a884-121">Aby ustawić `debug.javascript.usePreview` `true` przy użyciu interfejsu użytkownika vs Code, Otwórz **File**  >  **Preferences**  >  **Ustawienia** preferencji plików i Wyszukaj `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="2a884-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="2a884-122">Zaznacz pole wyboru **Użyj nowego debugera JavaScript w wersji zapoznawczej dla Node.js i programu Chrome**.</span><span class="sxs-lookup"><span data-stu-id="2a884-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="2a884-123">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2a884-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="2a884-124">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2a884-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="2a884-125">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="2a884-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2a884-126">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2a884-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="2a884-127">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="2a884-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2a884-128">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="2a884-128">Select **Yes**.</span></span>

1. <span data-ttu-id="2a884-129">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="2a884-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="2a884-130">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="2a884-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="2a884-131">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="2a884-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2a884-132">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="2a884-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="2a884-133">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a884-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2a884-134">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="2a884-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2a884-135">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="2a884-135">Select **Next**.</span></span>

   <span data-ttu-id="2a884-136">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="2a884-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2a884-137">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="2a884-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="2a884-138">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="2a884-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="2a884-139">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="2a884-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="2a884-140">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="2a884-140">Select **Next**.</span></span>

1. <span data-ttu-id="2a884-141">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="2a884-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="2a884-142">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2a884-142">Select **Create**.</span></span>

1. <span data-ttu-id="2a884-143">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="2a884-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="2a884-144">Uruchom **aplikację z**  >  przyciskiem Uruchom**debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="2a884-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="2a884-145">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="2a884-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2a884-146">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2a884-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
