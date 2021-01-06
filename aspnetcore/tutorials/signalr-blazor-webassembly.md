---
title: Używanie ASP.NET Core SignalR z hostowaną Blazor WebAssembly aplikacją
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797356"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="2b02f-103">Używanie ASP.NET Core SignalR z hostowaną Blazor WebAssembly aplikacją</span><span class="sxs-lookup"><span data-stu-id="2b02f-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="2b02f-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b02f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2b02f-105">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="2b02f-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="2b02f-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="2b02f-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b02f-107">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="2b02f-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2b02f-108">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="2b02f-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="2b02f-109">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="2b02f-110">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2b02f-111">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="2b02f-111">Add Razor component code for chat</span></span>

<span data-ttu-id="2b02f-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="2b02f-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2b02f-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2b02f-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="2b02f-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2b02f-116">[Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="2b02f-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="2b02f-119">Visual Studio dla komputerów Mac wersja 8,8 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="2b02f-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-121">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2b02f-122">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="2b02f-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-124">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="2b02f-125">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="2b02f-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-126">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="2b02f-127">Tworzenie projektu hostowanej Blazor WebAssembly aplikacji</span><span class="sxs-lookup"><span data-stu-id="2b02f-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="2b02f-128">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="2b02f-130">Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="2b02f-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="2b02f-131">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="2b02f-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="2b02f-132">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-132">Create a new project.</span></span>

1. <span data-ttu-id="2b02f-133">Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="2b02f-134">Wpisz `BlazorSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="2b02f-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="2b02f-135">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2b02f-136">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-136">Select **Create**.</span></span>

1. <span data-ttu-id="2b02f-137">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="2b02f-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="2b02f-138">W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2b02f-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="2b02f-139">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2b02f-141">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2b02f-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2b02f-142">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2b02f-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="2b02f-143">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="2b02f-144">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="2b02f-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-145">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2b02f-146">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="2b02f-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="2b02f-147">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2b02f-148">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="2b02f-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="2b02f-149">Wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="2b02f-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2b02f-150">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-150">Select **Next**.</span></span>

1. <span data-ttu-id="2b02f-151">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="2b02f-152">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="2b02f-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="2b02f-153">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-153">Select **Next**.</span></span>

1. <span data-ttu-id="2b02f-154">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="2b02f-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="2b02f-155">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-155">Select **Create**.</span></span>

   <span data-ttu-id="2b02f-156">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="2b02f-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2b02f-157">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="2b02f-158">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="2b02f-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-159">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2b02f-160">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2b02f-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="2b02f-161">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="2b02f-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-162">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2b02f-163">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2b02f-164">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2b02f-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2b02f-165">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="2b02f-166">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="2b02f-167">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2b02f-168">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="2b02f-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2b02f-170">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-171">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2b02f-172">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2b02f-173">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2b02f-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2b02f-174">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="2b02f-175">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu i wybierz polecenie **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="2b02f-176">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="2b02f-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-177">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2b02f-178">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="2b02f-179">Dodaj pakiet System. Text. encodings. Web</span><span class="sxs-lookup"><span data-stu-id="2b02f-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="2b02f-180">Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) z 5.0.0 w aplikacji ASP.NET Core 3,1, `BlazorSignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="2b02f-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="2b02f-181">Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5.</span><span class="sxs-lookup"><span data-stu-id="2b02f-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="2b02f-182">Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="2b02f-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="2b02f-183">Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorSignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-184">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2b02f-185">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2b02f-186">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2b02f-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2b02f-187">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="2b02f-188">W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="2b02f-189">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2b02f-190">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="2b02f-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2b02f-192">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-193">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2b02f-194">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2b02f-195">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="2b02f-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="2b02f-196">Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="2b02f-197">W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu i wybierz polecenie **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="2b02f-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="2b02f-198">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="2b02f-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-199">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2b02f-200">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2b02f-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="2b02f-201">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-201">Add a SignalR hub</span></span>

<span data-ttu-id="2b02f-202">W `BlazorSignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="2b02f-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="2b02f-203">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="2b02f-204">W `BlazorSignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="2b02f-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="2b02f-205">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="2b02f-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="2b02f-206">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b02f-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="2b02f-207">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2b02f-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="2b02f-208">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="2b02f-209">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="2b02f-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="2b02f-210">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b02f-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="2b02f-211">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2b02f-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="2b02f-212">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="2b02f-213">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="2b02f-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="2b02f-214">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="2b02f-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="2b02f-215">W `BlazorSignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="2b02f-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="2b02f-216">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="2b02f-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="2b02f-217">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="2b02f-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="2b02f-218">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="2b02f-218">Run the app</span></span>

<span data-ttu-id="2b02f-219">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="2b02f-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2b02f-220">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2b02f-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2b02f-221">W **Eksplorator rozwiązań** wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="2b02f-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="2b02f-222">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2b02f-223">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2b02f-224">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="2b02f-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2b02f-225">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="2b02f-225">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2b02f-227">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2b02f-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2b02f-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2b02f-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="2b02f-229">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="2b02f-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="2b02f-230">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="2b02f-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="2b02f-231">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2b02f-232">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2b02f-233">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="2b02f-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2b02f-234">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="2b02f-234">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2b02f-236">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2b02f-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2b02f-237">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="2b02f-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2b02f-238">Na pasku bocznym **rozwiązania** wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="2b02f-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="2b02f-239">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="2b02f-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="2b02f-240">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2b02f-241">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="2b02f-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2b02f-242">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="2b02f-242">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2b02f-244">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2b02f-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2b02f-245">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b02f-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2b02f-246">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="2b02f-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="2b02f-247">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="2b02f-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2b02f-248">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="2b02f-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="2b02f-249">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="2b02f-249">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2b02f-251">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2b02f-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2b02f-252">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="2b02f-252">Next steps</span></span>

<span data-ttu-id="2b02f-253">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="2b02f-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2b02f-254">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="2b02f-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2b02f-255">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="2b02f-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="2b02f-256">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="2b02f-257">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="2b02f-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2b02f-258">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="2b02f-258">Add Razor component code for chat</span></span>

<span data-ttu-id="2b02f-259">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="2b02f-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="2b02f-260"><xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="2b02f-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b02f-261">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2b02f-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="2b02f-262">SignalR negocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2b02f-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
