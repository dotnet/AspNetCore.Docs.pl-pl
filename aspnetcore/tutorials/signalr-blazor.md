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
ms.openlocfilehash: 50e5f2d5b1f9d0bf229bc57e6a1f599f9574b27c
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395230"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="df43d-103">Użyj ASP.NET Core SignalR z Blazor</span><span class="sxs-lookup"><span data-stu-id="df43d-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="df43d-104">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor .</span><span class="sxs-lookup"><span data-stu-id="df43d-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="df43d-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="df43d-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="df43d-106">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="df43d-106">Create a Blazor project</span></span>
> * <span data-ttu-id="df43d-107">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="df43d-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="df43d-108">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="df43d-109">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="df43d-110">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="df43d-110">Add Razor component code for chat</span></span>

<span data-ttu-id="df43d-111">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="df43d-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="df43d-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="df43d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="df43d-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="df43d-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="df43d-115">[Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="df43d-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="df43d-118">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="df43d-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-119">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="df43d-121">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="df43d-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="df43d-124">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="df43d-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-125">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="df43d-126">Tworzenie aplikacji hostowanej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="df43d-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="df43d-127">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="df43d-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df43d-129">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="df43d-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df43d-130">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="df43d-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="df43d-131">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-131">Create a new project.</span></span>

1. <span data-ttu-id="df43d-132">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="df43d-133">Wpisz `BlazorWebAssemblySignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="df43d-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="df43d-134">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="df43d-135">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-135">Select **Create**.</span></span>

1. <span data-ttu-id="df43d-136">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="df43d-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="df43d-137">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="df43d-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="df43d-138">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="df43d-140">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="df43d-141">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="df43d-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="df43d-142">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="df43d-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="df43d-143">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="df43d-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="df43d-144">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="df43d-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="df43d-145">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="df43d-146">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="df43d-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="df43d-147">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="df43d-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-148">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-149">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="df43d-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="df43d-150">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="df43d-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="df43d-151">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="df43d-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="df43d-152">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="df43d-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="df43d-153">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-153">Select **Next**.</span></span>

1. <span data-ttu-id="df43d-154">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="df43d-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="df43d-155">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="df43d-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="df43d-156">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-156">Select **Next**.</span></span>

1. <span data-ttu-id="df43d-157">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="df43d-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="df43d-158">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-158">Select **Create**.</span></span>

   <span data-ttu-id="df43d-159">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="df43d-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="df43d-160">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="df43d-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="df43d-161">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="df43d-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-162">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-163">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="df43d-164">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="df43d-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="df43d-165">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="df43d-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="df43d-166">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="df43d-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="df43d-167">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="df43d-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="df43d-169">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-170">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-171">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="df43d-172">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="df43d-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="df43d-173">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="df43d-174">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="df43d-174">Select **Install**.</span></span>

1. <span data-ttu-id="df43d-175">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="df43d-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="df43d-176">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="df43d-178">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="df43d-179">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-180">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-181">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-182">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-183">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="df43d-184">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="df43d-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="df43d-185">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="df43d-186">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="df43d-187">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-188">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-189">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="df43d-190">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="df43d-191">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="df43d-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="df43d-192">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="df43d-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="df43d-193">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x `BlazorWebAssemblySignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="df43d-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="df43d-194">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="df43d-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="df43d-195">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="df43d-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="df43d-196">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorWebAssemblySignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="df43d-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="df43d-198">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-199">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-200">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="df43d-201">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="df43d-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="df43d-202">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="df43d-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="df43d-203">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="df43d-203">Select **Install**.</span></span>

1. <span data-ttu-id="df43d-204">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="df43d-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="df43d-205">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="df43d-207">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df43d-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="df43d-208">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-209">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-210">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-211">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-212">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="df43d-213">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="df43d-214">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-215">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-216">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="df43d-217">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="df43d-218">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-218">Add a SignalR hub</span></span>

<span data-ttu-id="df43d-219">W `BlazorWebAssemblySignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="df43d-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="df43d-220">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="df43d-221">W `BlazorWebAssemblySignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="df43d-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="df43d-222">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="df43d-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="df43d-223">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="df43d-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="df43d-224">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="df43d-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="df43d-225">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="df43d-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="df43d-226">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="df43d-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="df43d-227">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="df43d-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="df43d-228">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="df43d-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="df43d-229">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="df43d-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="df43d-230">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="df43d-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="df43d-231">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="df43d-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="df43d-232">W `BlazorWebAssemblySignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="df43d-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="df43d-233">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="df43d-233">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="df43d-234">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="df43d-234">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="df43d-235">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="df43d-235">Run the app</span></span>

<span data-ttu-id="df43d-236">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="df43d-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="df43d-238">W **Eksplorator rozwiązań** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="df43d-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="df43d-239">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-240">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-241">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-242">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-244">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="df43d-246">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="df43d-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="df43d-247">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-248">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-249">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-250">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-250">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-252">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-253">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-254">Na pasku bocznym **rozwiązania** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="df43d-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="df43d-255">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-256">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-257">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-258">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-258">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-260">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-261">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="df43d-262">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df43d-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="df43d-263">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-264">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-265">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-265">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-267">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="df43d-268">Tworzenie Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="df43d-268">Create a Blazor Server app</span></span>

<span data-ttu-id="df43d-269">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="df43d-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df43d-271">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="df43d-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="df43d-272">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="df43d-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="df43d-273">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-273">Create a new project.</span></span>

1. <span data-ttu-id="df43d-274">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="df43d-275">Wpisz `BlazorServerSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="df43d-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="df43d-276">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="df43d-277">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-277">Select **Create**.</span></span>

1. <span data-ttu-id="df43d-278">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="df43d-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="df43d-279">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="df43d-281">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="df43d-282">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="df43d-283">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="df43d-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="df43d-284">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="df43d-285">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="df43d-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="df43d-286">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="df43d-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="df43d-287">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="df43d-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-288">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-289">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="df43d-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="df43d-290">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="df43d-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="df43d-291">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="df43d-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="df43d-292">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="df43d-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="df43d-293">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-293">Select **Next**.</span></span>

1. <span data-ttu-id="df43d-294">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="df43d-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="df43d-295">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="df43d-295">Select **Next**.</span></span>

1. <span data-ttu-id="df43d-296">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="df43d-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="df43d-297">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="df43d-297">Select **Create**.</span></span>

   <span data-ttu-id="df43d-298">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="df43d-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="df43d-299">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="df43d-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="df43d-300">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="df43d-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-301">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-302">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="df43d-303">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="df43d-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="df43d-304">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="df43d-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="df43d-305">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="df43d-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="df43d-307">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-308">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-309">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="df43d-310">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="df43d-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="df43d-311">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="df43d-312">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="df43d-312">Select **Install**.</span></span>

1. <span data-ttu-id="df43d-313">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="df43d-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="df43d-314">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="df43d-316">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="df43d-317">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-318">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-319">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-320">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-321">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="df43d-322">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="df43d-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="df43d-323">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="df43d-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="df43d-324">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="df43d-325">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-326">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-327">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="df43d-328">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="df43d-329">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="df43d-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="df43d-330">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="df43d-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="df43d-331">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="df43d-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="df43d-332">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="df43d-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="df43d-333">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="df43d-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="df43d-334">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do projektu, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="df43d-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="df43d-336">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-337">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-338">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="df43d-339">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="df43d-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="df43d-340">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="df43d-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="df43d-341">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="df43d-341">Select **Install**.</span></span>

1. <span data-ttu-id="df43d-342">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="df43d-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="df43d-343">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="df43d-345">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df43d-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="df43d-346">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-347">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-348">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="df43d-349">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="df43d-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="df43d-350">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="df43d-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="df43d-351">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="df43d-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="df43d-352">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="df43d-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-353">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="df43d-354">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="df43d-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="df43d-355">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="df43d-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="df43d-356">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-356">Add a SignalR hub</span></span>

<span data-ttu-id="df43d-357">Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="df43d-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="df43d-358">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="df43d-359">Otwórz plik `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="df43d-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="df43d-360">Dodaj przestrzenie nazw dla <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> i `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="df43d-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="df43d-361">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="df43d-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="df43d-362">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="df43d-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="df43d-363">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="df43d-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="df43d-364">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="df43d-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="df43d-365">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="df43d-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="df43d-366">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="df43d-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="df43d-367">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="df43d-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="df43d-368">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="df43d-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="df43d-369">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="df43d-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="df43d-370">Otwórz plik `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="df43d-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="df43d-371">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="df43d-371">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="df43d-372">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="df43d-372">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="df43d-373">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="df43d-373">Run the app</span></span>

<span data-ttu-id="df43d-374">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="df43d-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df43d-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df43d-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="df43d-376">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-377">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-378">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-379">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-379">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-381">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="df43d-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="df43d-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="df43d-383">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-384">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-385">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-386">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-386">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-388">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="df43d-389">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="df43d-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="df43d-390">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="df43d-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="df43d-391">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-392">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-393">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-393">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-395">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df43d-396">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="df43d-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="df43d-397">W powłoce poleceń z folderu projektu wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="df43d-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="df43d-398">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="df43d-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="df43d-399">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="df43d-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="df43d-400">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="df43d-400">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="df43d-402">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="df43d-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="df43d-403">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="df43d-403">Next steps</span></span>

<span data-ttu-id="df43d-404">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="df43d-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="df43d-405">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="df43d-405">Create a Blazor project</span></span>
> * <span data-ttu-id="df43d-406">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="df43d-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="df43d-407">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="df43d-408">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="df43d-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="df43d-409">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="df43d-409">Add Razor component code for chat</span></span>

<span data-ttu-id="df43d-410">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="df43d-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="df43d-411"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="df43d-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df43d-412">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="df43d-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="df43d-413">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="df43d-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
