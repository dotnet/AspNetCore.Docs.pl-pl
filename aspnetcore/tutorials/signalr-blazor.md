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
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536405"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="1b297-103">Użyj ASP.NET Core SignalR z Blazor</span><span class="sxs-lookup"><span data-stu-id="1b297-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="1b297-104">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor .</span><span class="sxs-lookup"><span data-stu-id="1b297-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="1b297-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="1b297-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1b297-106">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="1b297-106">Create a Blazor project</span></span>
> * <span data-ttu-id="1b297-107">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1b297-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="1b297-108">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="1b297-109">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1b297-110">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1b297-110">Add Razor component code for chat</span></span>

<span data-ttu-id="1b297-111">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="1b297-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="1b297-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b297-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1b297-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="1b297-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1b297-115">[Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="1b297-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1b297-118">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="1b297-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-119">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1b297-121">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="1b297-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1b297-124">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="1b297-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-125">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="1b297-126">Tworzenie aplikacji hostowanej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1b297-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="1b297-127">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1b297-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1b297-129">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1b297-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1b297-130">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1b297-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="1b297-131">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-131">Create a new project.</span></span>

1. <span data-ttu-id="1b297-132">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="1b297-133">Wpisz `BlazorWebAssemblySignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="1b297-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="1b297-134">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1b297-135">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-135">Select **Create**.</span></span>

1. <span data-ttu-id="1b297-136">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b297-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="1b297-137">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1b297-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="1b297-138">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1b297-140">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="1b297-141">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1b297-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="1b297-142">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="1b297-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="1b297-143">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1b297-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="1b297-144">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="1b297-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="1b297-145">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="1b297-146">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="1b297-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="1b297-147">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="1b297-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-148">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-149">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="1b297-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="1b297-150">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="1b297-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1b297-151">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="1b297-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="1b297-152">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b297-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1b297-153">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-153">Select **Next**.</span></span>

1. <span data-ttu-id="1b297-154">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="1b297-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="1b297-155">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1b297-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="1b297-156">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-156">Select **Next**.</span></span>

1. <span data-ttu-id="1b297-157">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="1b297-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="1b297-158">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-158">Select **Create**.</span></span>

   <span data-ttu-id="1b297-159">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="1b297-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1b297-160">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1b297-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="1b297-161">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="1b297-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-162">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-163">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="1b297-164">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="1b297-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="1b297-165">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1b297-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="1b297-166">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="1b297-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="1b297-167">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1b297-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1b297-169">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-170">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-171">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1b297-172">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="1b297-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="1b297-173">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="1b297-174">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="1b297-174">Select **Install**.</span></span>

1. <span data-ttu-id="1b297-175">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="1b297-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="1b297-176">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1b297-178">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="1b297-179">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-180">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-181">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-182">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-183">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1b297-184">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="1b297-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="1b297-185">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="1b297-186">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="1b297-187">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-188">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-189">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="1b297-190">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="1b297-191">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="1b297-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="1b297-192">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="1b297-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="1b297-193">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x `BlazorWebAssemblySignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="1b297-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="1b297-194">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="1b297-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="1b297-195">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="1b297-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="1b297-196">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorWebAssemblySignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1b297-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1b297-198">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-199">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-200">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="1b297-201">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="1b297-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="1b297-202">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="1b297-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="1b297-203">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="1b297-203">Select **Install**.</span></span>

1. <span data-ttu-id="1b297-204">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="1b297-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="1b297-205">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1b297-207">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1b297-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="1b297-208">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-209">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-210">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-211">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-212">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="1b297-213">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="1b297-214">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-215">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-216">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="1b297-217">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="1b297-218">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-218">Add a SignalR hub</span></span>

<span data-ttu-id="1b297-219">W `BlazorWebAssemblySignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="1b297-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="1b297-220">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="1b297-221">W `BlazorWebAssemblySignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="1b297-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="1b297-222">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="1b297-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="1b297-223">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b297-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="1b297-224">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1b297-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1b297-225">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="1b297-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1b297-226">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="1b297-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="1b297-227">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b297-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="1b297-228">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1b297-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1b297-229">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="1b297-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1b297-230">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="1b297-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="1b297-231">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1b297-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="1b297-232">W `BlazorWebAssemblySignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="1b297-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="1b297-233">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1b297-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="1b297-234">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1b297-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="1b297-235">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b297-235">Run the app</span></span>

<span data-ttu-id="1b297-236">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="1b297-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1b297-238">W **Eksplorator rozwiązań** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="1b297-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="1b297-239">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-240">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-241">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-242">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-244">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1b297-246">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="1b297-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="1b297-247">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-248">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-249">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-250">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-250">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-252">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-253">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-254">Na pasku bocznym **rozwiązania** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="1b297-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="1b297-255">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-256">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-257">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-258">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-258">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-260">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-261">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="1b297-262">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1b297-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="1b297-263">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-264">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-265">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-265">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-267">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="1b297-268">Tworzenie Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b297-268">Create a Blazor Server app</span></span>

<span data-ttu-id="1b297-269">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1b297-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1b297-271">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1b297-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1b297-272">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="1b297-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="1b297-273">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-273">Create a new project.</span></span>

1. <span data-ttu-id="1b297-274">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="1b297-275">Wpisz `BlazorServerSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="1b297-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="1b297-276">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1b297-277">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-277">Select **Create**.</span></span>

1. <span data-ttu-id="1b297-278">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b297-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="1b297-279">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1b297-281">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="1b297-282">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="1b297-283">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="1b297-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="1b297-284">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="1b297-285">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="1b297-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="1b297-286">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="1b297-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="1b297-287">Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .</span><span class="sxs-lookup"><span data-stu-id="1b297-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-288">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-289">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="1b297-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="1b297-290">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="1b297-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1b297-291">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="1b297-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="1b297-292">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b297-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1b297-293">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-293">Select **Next**.</span></span>

1. <span data-ttu-id="1b297-294">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="1b297-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="1b297-295">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b297-295">Select **Next**.</span></span>

1. <span data-ttu-id="1b297-296">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="1b297-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="1b297-297">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b297-297">Select **Create**.</span></span>

   <span data-ttu-id="1b297-298">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="1b297-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1b297-299">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1b297-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="1b297-300">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="1b297-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-301">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-302">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="1b297-303">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b297-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="1b297-304">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="1b297-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="1b297-305">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1b297-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1b297-307">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-308">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-309">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1b297-310">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="1b297-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="1b297-311">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="1b297-312">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="1b297-312">Select **Install**.</span></span>

1. <span data-ttu-id="1b297-313">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="1b297-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="1b297-314">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1b297-316">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="1b297-317">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-318">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-319">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-320">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-321">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="1b297-322">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="1b297-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="1b297-323">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b297-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="1b297-324">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="1b297-325">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-326">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-327">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="1b297-328">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="1b297-329">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="1b297-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="1b297-330">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="1b297-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="1b297-331">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="1b297-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="1b297-332">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="1b297-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="1b297-333">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="1b297-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="1b297-334">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do projektu, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="1b297-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="1b297-336">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-337">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-338">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="1b297-339">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="1b297-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="1b297-340">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="1b297-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="1b297-341">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="1b297-341">Select **Install**.</span></span>

1. <span data-ttu-id="1b297-342">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="1b297-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="1b297-343">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="1b297-345">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1b297-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="1b297-346">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-347">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-348">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="1b297-349">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="1b297-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="1b297-350">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="1b297-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="1b297-351">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="1b297-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="1b297-352">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="1b297-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-353">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="1b297-354">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b297-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="1b297-355">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="1b297-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="1b297-356">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-356">Add a SignalR hub</span></span>

<span data-ttu-id="1b297-357">Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="1b297-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="1b297-358">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="1b297-359">Otwórz plik `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="1b297-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="1b297-360">Dodaj przestrzenie nazw dla <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> i `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="1b297-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="1b297-361">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b297-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="1b297-362">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1b297-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1b297-363">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="1b297-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1b297-364">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="1b297-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="1b297-365">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1b297-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="1b297-366">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1b297-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="1b297-367">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="1b297-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="1b297-368">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="1b297-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="1b297-369">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1b297-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="1b297-370">Otwórz plik `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="1b297-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="1b297-371">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1b297-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="1b297-372">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="1b297-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="1b297-373">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b297-373">Run the app</span></span>

<span data-ttu-id="1b297-374">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="1b297-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1b297-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b297-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1b297-376">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-377">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-378">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-379">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-379">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-381">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1b297-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b297-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1b297-383">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-384">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-385">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-386">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-386">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-388">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b297-389">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b297-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1b297-390">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="1b297-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="1b297-391">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-392">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-393">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-393">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-395">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="1b297-396">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b297-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="1b297-397">W powłoce poleceń z folderu projektu wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="1b297-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="1b297-398">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="1b297-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="1b297-399">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="1b297-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="1b297-400">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="1b297-400">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="1b297-402">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="1b297-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="1b297-403">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="1b297-403">Next steps</span></span>

<span data-ttu-id="1b297-404">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="1b297-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1b297-405">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="1b297-405">Create a Blazor project</span></span>
> * <span data-ttu-id="1b297-406">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="1b297-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="1b297-407">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="1b297-408">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="1b297-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="1b297-409">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="1b297-409">Add Razor component code for chat</span></span>

<span data-ttu-id="1b297-410">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="1b297-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="1b297-411"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="1b297-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b297-412">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1b297-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="1b297-413">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b297-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
