---
title: Użyj ASP.NET Core SignalR z Blazor
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 5bf80bd9147b6210fd2b115f7c009cc518f6f5cb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99579127"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="d0098-103">Użyj ASP.NET Core SignalR z Blazor</span><span class="sxs-lookup"><span data-stu-id="d0098-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="d0098-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0098-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d0098-105">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor .</span><span class="sxs-lookup"><span data-stu-id="d0098-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="d0098-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="d0098-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d0098-107">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="d0098-107">Create a Blazor project</span></span>
> * <span data-ttu-id="d0098-108">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="d0098-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="d0098-109">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="d0098-110">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d0098-111">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="d0098-111">Add Razor component code for chat</span></span>

<span data-ttu-id="d0098-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="d0098-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="d0098-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0098-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0098-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d0098-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0098-116">[Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="d0098-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d0098-119">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="d0098-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d0098-122">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="d0098-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-124">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d0098-125">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="d0098-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-126">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="d0098-127">Tworzenie aplikacji hostowanej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d0098-127">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="d0098-128">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="d0098-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d0098-130">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="d0098-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d0098-131">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="d0098-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d0098-132">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-132">Create a new project.</span></span>

1. <span data-ttu-id="d0098-133">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d0098-134">Wpisz `BlazorWebAssemblySignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="d0098-134">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d0098-135">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d0098-136">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-136">Select **Create**.</span></span>

1. <span data-ttu-id="d0098-137">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d0098-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="d0098-138">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="d0098-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="d0098-139">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d0098-141">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="d0098-142">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d0098-142">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="d0098-143">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="d0098-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d0098-144">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="d0098-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="d0098-145">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d0098-146">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="d0098-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d0098-147">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="d0098-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-148">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-149">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d0098-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d0098-150">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="d0098-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d0098-151">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="d0098-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d0098-152">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d0098-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d0098-153">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-153">Select **Next**.</span></span>

1. <span data-ttu-id="d0098-154">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="d0098-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d0098-155">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="d0098-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="d0098-156">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-156">Select **Next**.</span></span>

1. <span data-ttu-id="d0098-157">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="d0098-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="d0098-158">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-158">Select **Create**.</span></span>

   <span data-ttu-id="d0098-159">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="d0098-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d0098-160">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="d0098-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d0098-161">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="d0098-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-162">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-163">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="d0098-164">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="d0098-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="d0098-165">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="d0098-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d0098-166">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="d0098-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d0098-167">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="d0098-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d0098-169">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-170">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-171">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d0098-172">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d0098-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d0098-173">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d0098-174">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="d0098-174">Select **Install**.</span></span>

1. <span data-ttu-id="d0098-175">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="d0098-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d0098-176">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d0098-178">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d0098-179">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-180">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-181">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-182">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-183">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d0098-184">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="d0098-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d0098-185">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d0098-186">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="d0098-187">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-188">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-189">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d0098-190">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d0098-191">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="d0098-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d0098-192">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="d0098-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d0098-193">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x `BlazorWebAssemblySignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="d0098-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d0098-194">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d0098-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d0098-195">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d0098-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d0098-196">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorWebAssemblySignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="d0098-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d0098-198">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-199">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-200">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d0098-201">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d0098-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d0098-202">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="d0098-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d0098-203">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="d0098-203">Select **Install**.</span></span>

1. <span data-ttu-id="d0098-204">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="d0098-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d0098-205">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d0098-207">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="d0098-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d0098-208">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-209">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-210">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-211">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-212">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d0098-213">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d0098-214">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-215">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-216">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d0098-217">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d0098-218">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-218">Add a SignalR hub</span></span>

<span data-ttu-id="d0098-219">W `BlazorWebAssemblySignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d0098-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d0098-220">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d0098-221">W `BlazorWebAssemblySignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="d0098-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d0098-222">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="d0098-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d0098-223">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0098-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="d0098-224">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d0098-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d0098-225">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="d0098-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d0098-226">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="d0098-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d0098-227">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0098-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="d0098-228">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d0098-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d0098-229">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="d0098-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d0098-230">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="d0098-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d0098-231">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="d0098-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d0098-232">W `BlazorWebAssemblySignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="d0098-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d0098-233">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d0098-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d0098-234">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d0098-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d0098-235">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0098-235">Run the app</span></span>

<span data-ttu-id="d0098-236">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="d0098-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d0098-238">W **Eksplorator rozwiązań** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="d0098-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d0098-239">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-240">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-241">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-242">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-244">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d0098-246">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-246">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-247">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-248">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-249">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-251">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-252">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-252">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-253">Na pasku bocznym **rozwiązania** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="d0098-253">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d0098-254">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-254">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-255">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-255">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-256">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-256">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-257">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-257">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-259">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-259">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-260">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-260">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d0098-261">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="d0098-261">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="d0098-262">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-262">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-263">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-263">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-264">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-264">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-266">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-266">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="d0098-267">Tworzenie Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0098-267">Create a Blazor Server app</span></span>

<span data-ttu-id="d0098-268">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="d0098-268">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-269">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d0098-270">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="d0098-270">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d0098-271">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="d0098-271">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d0098-272">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-272">Create a new project.</span></span>

1. <span data-ttu-id="d0098-273">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-273">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d0098-274">Wpisz `BlazorServerSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="d0098-274">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d0098-275">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-275">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d0098-276">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-276">Select **Create**.</span></span>

1. <span data-ttu-id="d0098-277">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d0098-277">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="d0098-278">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-278">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-279">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-279">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d0098-280">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-280">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="d0098-281">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-281">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d0098-282">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="d0098-282">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="d0098-283">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-283">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d0098-284">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="d0098-284">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d0098-285">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="d0098-285">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-286">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-287">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d0098-287">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d0098-288">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="d0098-288">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d0098-289">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="d0098-289">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d0098-290">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="d0098-290">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d0098-291">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-291">Select **Next**.</span></span>

1. <span data-ttu-id="d0098-292">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="d0098-292">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d0098-293">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="d0098-293">Select **Next**.</span></span>

1. <span data-ttu-id="d0098-294">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="d0098-294">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="d0098-295">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="d0098-295">Select **Create**.</span></span>

   <span data-ttu-id="d0098-296">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="d0098-296">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d0098-297">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="d0098-297">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d0098-298">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="d0098-298">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-299">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-299">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-300">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-300">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="d0098-301">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="d0098-301">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d0098-302">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="d0098-302">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d0098-303">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="d0098-303">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-304">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-304">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d0098-305">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-305">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-306">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-306">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-307">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-307">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d0098-308">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d0098-308">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d0098-309">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-309">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d0098-310">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="d0098-310">Select **Install**.</span></span>

1. <span data-ttu-id="d0098-311">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="d0098-311">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d0098-312">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-312">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-313">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d0098-314">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-314">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d0098-315">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-315">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-316">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-317">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-317">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-318">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-318">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-319">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-319">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d0098-320">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="d0098-320">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d0098-321">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0098-321">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d0098-322">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-322">Select **Add Package**.</span></span>

1. <span data-ttu-id="d0098-323">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-323">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-324">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-324">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-325">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-325">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d0098-326">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-326">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d0098-327">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="d0098-327">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d0098-328">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="d0098-328">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d0098-329">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="d0098-329">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d0098-330">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="d0098-330">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d0098-331">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d0098-331">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d0098-332">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do projektu, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="d0098-332">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-333">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d0098-334">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-334">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-335">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-335">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-336">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-336">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d0098-337">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d0098-337">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d0098-338">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="d0098-338">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d0098-339">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="d0098-339">Select **Install**.</span></span>

1. <span data-ttu-id="d0098-340">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="d0098-340">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d0098-341">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-341">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-342">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-342">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d0098-343">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="d0098-343">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d0098-344">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-344">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-345">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-345">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-346">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-346">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d0098-347">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="d0098-347">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d0098-348">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="d0098-348">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d0098-349">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="d0098-349">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d0098-350">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="d0098-350">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-351">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-351">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d0098-352">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0098-352">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d0098-353">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="d0098-353">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d0098-354">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-354">Add a SignalR hub</span></span>

<span data-ttu-id="d0098-355">Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="d0098-355">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d0098-356">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-356">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d0098-357">Otwórz plik `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d0098-357">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d0098-358">Dodaj przestrzenie nazw dla <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> i `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="d0098-358">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d0098-359">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0098-359">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d0098-360">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d0098-360">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d0098-361">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="d0098-361">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d0098-362">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="d0098-362">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d0098-363">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0098-363">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d0098-364">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d0098-364">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d0098-365">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="d0098-365">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d0098-366">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="d0098-366">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d0098-367">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="d0098-367">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d0098-368">Otwórz plik `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d0098-368">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d0098-369">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d0098-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d0098-370">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="d0098-370">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d0098-371">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0098-371">Run the app</span></span>

<span data-ttu-id="d0098-372">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="d0098-372">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0098-373">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0098-373">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d0098-374">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-374">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-375">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-375">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-376">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-376">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-377">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-377">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-379">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-379">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d0098-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d0098-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d0098-381">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-381">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-382">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-382">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-383">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-383">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-384">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-384">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-386">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-386">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d0098-387">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d0098-387">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d0098-388">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="d0098-388">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d0098-389">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-389">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-390">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-390">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-391">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-391">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-393">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-393">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d0098-394">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0098-394">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d0098-395">W powłoce poleceń z folderu projektu wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="d0098-395">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="d0098-396">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="d0098-396">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d0098-397">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="d0098-397">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d0098-398">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="d0098-398">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d0098-400">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d0098-400">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="d0098-401">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="d0098-401">Next steps</span></span>

<span data-ttu-id="d0098-402">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="d0098-402">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d0098-403">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="d0098-403">Create a Blazor project</span></span>
> * <span data-ttu-id="d0098-404">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="d0098-404">Add the SignalR client library</span></span>
> * <span data-ttu-id="d0098-405">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-405">Add a SignalR hub</span></span>
> * <span data-ttu-id="d0098-406">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d0098-406">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d0098-407">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="d0098-407">Add Razor component code for chat</span></span>

<span data-ttu-id="d0098-408">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="d0098-408">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="d0098-409"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="d0098-409"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0098-410">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="d0098-410">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="d0098-411">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0098-411">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
