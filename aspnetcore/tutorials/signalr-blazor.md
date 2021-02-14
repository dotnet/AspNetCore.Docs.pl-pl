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
ms.openlocfilehash: 355db5ad5462747be0058096bc0132ed1071f290
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280994"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="f2154-103">Użyj ASP.NET Core SignalR z Blazor</span><span class="sxs-lookup"><span data-stu-id="f2154-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="f2154-104">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor .</span><span class="sxs-lookup"><span data-stu-id="f2154-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="f2154-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="f2154-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2154-106">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="f2154-106">Create a Blazor project</span></span>
> * <span data-ttu-id="f2154-107">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="f2154-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="f2154-108">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="f2154-109">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f2154-110">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="f2154-110">Add Razor component code for chat</span></span>

<span data-ttu-id="f2154-111">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="f2154-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="f2154-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2154-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f2154-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="f2154-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2154-115">[Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="f2154-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f2154-118">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="f2154-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-119">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2154-121">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="f2154-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-123">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f2154-124">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="f2154-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-125">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="f2154-126">Tworzenie aplikacji hostowanej Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f2154-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="f2154-127">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="f2154-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f2154-129">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="f2154-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f2154-130">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="f2154-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f2154-131">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-131">Create a new project.</span></span>

1. <span data-ttu-id="f2154-132">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f2154-133">Wpisz `BlazorWebAssemblySignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="f2154-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f2154-134">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f2154-135">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-135">Select **Create**.</span></span>

1. <span data-ttu-id="f2154-136">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="f2154-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="f2154-137">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="f2154-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f2154-138">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f2154-140">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="f2154-141">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f2154-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

   <span data-ttu-id="f2154-142">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="f2154-142">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f2154-143">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="f2154-143">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="f2154-144">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-144">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f2154-145">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="f2154-145">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f2154-146">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="f2154-146">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-147">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-147">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-148">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f2154-148">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f2154-149">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="f2154-149">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f2154-150">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="f2154-150">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f2154-151">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="f2154-151">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f2154-152">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-152">Select **Next**.</span></span>

1. <span data-ttu-id="f2154-153">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="f2154-153">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f2154-154">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="f2154-154">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="f2154-155">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-155">Select **Next**.</span></span>

1. <span data-ttu-id="f2154-156">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorWebAssemblySignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f2154-156">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="f2154-157">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-157">Select **Create**.</span></span>

   <span data-ttu-id="f2154-158">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="f2154-158">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f2154-159">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="f2154-159">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f2154-160">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f2154-160">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-161">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-161">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-162">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-162">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="f2154-163">`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="f2154-163">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="f2154-164">`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="f2154-164">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="f2154-165">Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="f2154-165">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f2154-166">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="f2154-166">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-167">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-167">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f2154-168">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-168">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-169">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-169">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-170">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-170">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f2154-171">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="f2154-171">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f2154-172">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-172">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f2154-173">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="f2154-173">Select **Install**.</span></span>

1. <span data-ttu-id="f2154-174">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="f2154-174">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f2154-175">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-175">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-176">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-176">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f2154-177">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-177">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f2154-178">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-178">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-179">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-180">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-180">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-181">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-181">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-182">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-182">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f2154-183">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="f2154-183">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f2154-184">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-184">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f2154-185">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-185">Select **Add Package**.</span></span>

1. <span data-ttu-id="f2154-186">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-186">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-187">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-187">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-188">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-188">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f2154-189">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-189">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f2154-190">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="f2154-190">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f2154-191">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f2154-191">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f2154-192">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x `BlazorWebAssemblySignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f2154-192">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f2154-193">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f2154-193">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f2154-194">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f2154-194">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f2154-195">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorWebAssemblySignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="f2154-195">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-196">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f2154-197">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-197">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-198">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-198">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-199">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-199">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f2154-200">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="f2154-200">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f2154-201">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="f2154-201">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f2154-202">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="f2154-202">Select **Install**.</span></span>

1. <span data-ttu-id="f2154-203">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="f2154-203">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f2154-204">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-204">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-205">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f2154-206">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="f2154-206">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f2154-207">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-207">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-208">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-209">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-209">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-210">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-210">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-211">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-211">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f2154-212">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-212">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f2154-213">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-213">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-214">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-214">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-215">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-215">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="f2154-216">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-216">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f2154-217">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-217">Add a SignalR hub</span></span>

<span data-ttu-id="f2154-218">W `BlazorWebAssemblySignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="f2154-218">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f2154-219">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-219">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f2154-220">W `BlazorWebAssemblySignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="f2154-220">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f2154-221">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="f2154-221">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f2154-222">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2154-222">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="f2154-223">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f2154-223">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f2154-224">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="f2154-224">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f2154-225">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="f2154-225">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f2154-226">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2154-226">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="f2154-227">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f2154-227">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f2154-228">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="f2154-228">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f2154-229">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="f2154-229">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f2154-230">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="f2154-230">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f2154-231">W `BlazorWebAssemblySignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="f2154-231">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f2154-232">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f2154-232">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f2154-233">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f2154-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f2154-234">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f2154-234">Run the app</span></span>

<span data-ttu-id="f2154-235">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="f2154-235">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-236">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f2154-237">W **Eksplorator rozwiązań** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="f2154-237">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f2154-238">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-238">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-239">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-239">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-240">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-240">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-241">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-241">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-243">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-243">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-244">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-244">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f2154-245">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-245">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-246">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-246">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-247">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-247">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-248">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-248">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-250">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-250">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-251">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-252">Na pasku bocznym **rozwiązania** wybierz `BlazorWebAssemblySignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="f2154-252">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="f2154-253">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-253">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-254">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-254">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-255">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-255">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-256">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-256">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-258">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-258">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-259">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-259">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f2154-260">W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="f2154-260">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="f2154-261">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-261">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-262">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-262">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-263">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-263">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-265">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-265">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="f2154-266">Tworzenie Blazor Server aplikacji</span><span class="sxs-lookup"><span data-stu-id="f2154-266">Create a Blazor Server app</span></span>

<span data-ttu-id="f2154-267">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="f2154-267">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-268">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f2154-269">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="f2154-269">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f2154-270">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="f2154-270">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="f2154-271">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-271">Create a new project.</span></span>

1. <span data-ttu-id="f2154-272">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-272">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f2154-273">Wpisz `BlazorServerSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="f2154-273">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f2154-274">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-274">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f2154-275">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-275">Select **Create**.</span></span>

1. <span data-ttu-id="f2154-276">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="f2154-276">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="f2154-277">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-277">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-278">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-278">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f2154-279">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-279">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="f2154-280">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-280">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f2154-281">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="f2154-281">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="f2154-282">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-282">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f2154-283">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="f2154-283">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f2154-284">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="f2154-284">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-285">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-285">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-286">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f2154-286">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f2154-287">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="f2154-287">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f2154-288">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="f2154-288">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f2154-289">Wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="f2154-289">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f2154-290">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-290">Select **Next**.</span></span>

1. <span data-ttu-id="f2154-291">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="f2154-291">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f2154-292">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f2154-292">Select **Next**.</span></span>

1. <span data-ttu-id="f2154-293">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorServerSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="f2154-293">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="f2154-294">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f2154-294">Select **Create**.</span></span>

   <span data-ttu-id="f2154-295">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="f2154-295">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f2154-296">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="f2154-296">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f2154-297">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="f2154-297">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-298">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-298">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-299">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-299">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="f2154-300">`-o|--output`Opcja powoduje utworzenie folderu dla projektu.</span><span class="sxs-lookup"><span data-stu-id="f2154-300">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="f2154-301">Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="f2154-301">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f2154-302">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="f2154-302">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-303">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-303">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f2154-304">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-304">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-305">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-305">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-306">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-306">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f2154-307">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet.</span><span class="sxs-lookup"><span data-stu-id="f2154-307">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f2154-308">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-308">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f2154-309">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="f2154-309">Select **Install**.</span></span>

1. <span data-ttu-id="f2154-310">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="f2154-310">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f2154-311">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-311">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-312">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-312">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f2154-313">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-313">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f2154-314">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-314">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-315">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-315">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-316">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-316">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-317">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-317">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-318">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-318">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f2154-319">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu.</span><span class="sxs-lookup"><span data-stu-id="f2154-319">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="f2154-320">Ustaw wersję na zgodną z udostępnioną strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f2154-320">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="f2154-321">Wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-321">Select **Add Package**.</span></span>

1. <span data-ttu-id="f2154-322">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-322">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-323">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-323">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-324">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-324">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="f2154-325">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-325">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="f2154-326">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="f2154-326">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="f2154-327">*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*</span><span class="sxs-lookup"><span data-stu-id="f2154-327">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="f2154-328">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="f2154-328">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="f2154-329">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="f2154-329">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="f2154-330">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="f2154-330">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="f2154-331">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do projektu, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="f2154-331">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-332">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f2154-333">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-333">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-334">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-334">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-335">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-335">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f2154-336">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet.</span><span class="sxs-lookup"><span data-stu-id="f2154-336">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="f2154-337">Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu.</span><span class="sxs-lookup"><span data-stu-id="f2154-337">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="f2154-338">Wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="f2154-338">Select **Install**.</span></span>

1. <span data-ttu-id="f2154-339">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="f2154-339">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f2154-340">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-340">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-341">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-341">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f2154-342">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="f2154-342">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f2154-343">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-343">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-344">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-344">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-345">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-345">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f2154-346">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="f2154-346">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f2154-347">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="f2154-347">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="f2154-348">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="f2154-348">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="f2154-349">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="f2154-349">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-350">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-350">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f2154-351">W powłoce poleceń z folderu projektu wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="f2154-351">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="f2154-352">Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.</span><span class="sxs-lookup"><span data-stu-id="f2154-352">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f2154-353">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-353">Add a SignalR hub</span></span>

<span data-ttu-id="f2154-354">Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="f2154-354">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f2154-355">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-355">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f2154-356">Otwórz plik `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="f2154-356">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f2154-357">Dodaj przestrzenie nazw dla <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> i `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="f2154-357">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f2154-358">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2154-358">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f2154-359">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f2154-359">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f2154-360">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="f2154-360">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f2154-361">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="f2154-361">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f2154-362">Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2154-362">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="f2154-363">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f2154-363">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f2154-364">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="f2154-364">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f2154-365">Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="f2154-365">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f2154-366">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="f2154-366">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f2154-367">Otwórz plik `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="f2154-367">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f2154-368">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f2154-368">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f2154-369">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="f2154-369">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="f2154-370">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f2154-370">Run the app</span></span>

<span data-ttu-id="f2154-371">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="f2154-371">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2154-372">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2154-372">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f2154-373">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-373">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-374">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-374">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-375">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-375">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-376">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-376">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-378">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-378">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2154-379">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2154-379">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f2154-380">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-380">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-381">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-381">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-382">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-382">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-383">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-383">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-385">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-385">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f2154-386">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f2154-386">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f2154-387">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="f2154-387">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f2154-388">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-388">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-389">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-389">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-390">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-390">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-392">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-392">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f2154-393">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f2154-393">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f2154-394">W powłoce poleceń z folderu projektu wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="f2154-394">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="f2154-395">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="f2154-395">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f2154-396">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="f2154-396">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f2154-397">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="f2154-397">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="f2154-399">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f2154-399">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="f2154-400">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="f2154-400">Next steps</span></span>

<span data-ttu-id="f2154-401">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="f2154-401">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f2154-402">Tworzenie Blazor projektu</span><span class="sxs-lookup"><span data-stu-id="f2154-402">Create a Blazor project</span></span>
> * <span data-ttu-id="f2154-403">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="f2154-403">Add the SignalR client library</span></span>
> * <span data-ttu-id="f2154-404">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-404">Add a SignalR hub</span></span>
> * <span data-ttu-id="f2154-405">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="f2154-405">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f2154-406">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="f2154-406">Add Razor component code for chat</span></span>

<span data-ttu-id="f2154-407">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="f2154-407">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="f2154-408"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="f2154-408"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2154-409">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f2154-409">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="f2154-410">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f2154-410">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
