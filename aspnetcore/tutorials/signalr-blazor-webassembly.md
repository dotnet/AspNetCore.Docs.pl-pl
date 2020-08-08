---
title: Użyj ASP.NET Core SignalR zBlazor WebAssembly
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
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
ms.openlocfilehash: 3d7ae49142849c589a1a20c33d30e87747ad1935
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021630"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="0ef7c-103">Użyj ASP.NET Core SignalR zBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0ef7c-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="0ef7c-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ef7c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0ef7c-105">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="0ef7c-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ef7c-107">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ef7c-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0ef7c-108">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="0ef7c-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="0ef7c-109">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="0ef7c-110">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0ef7c-111">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="0ef7c-111">Add Razor component code for chat</span></span>

<span data-ttu-id="0ef7c-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0ef7c-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ef7c-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0ef7c-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="0ef7c-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ef7c-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ef7c-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0ef7c-116">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="0ef7c-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ef7c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ef7c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ef7c-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ef7c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="0ef7c-119">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="0ef7c-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0ef7c-120">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ef7c-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="0ef7c-121">Tworzenie projektu hostowanej Blazor WebAssembly aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ef7c-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="0ef7c-122">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ef7c-123">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ef7c-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="0ef7c-124">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="0ef7c-125">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-125">Create a new project.</span></span>

1. <span data-ttu-id="0ef7c-126">Wybierz pozycję \*\* Blazor aplikacja\*\* i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="0ef7c-127">Wpisz `BlazorSignalRApp` wartość w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="0ef7c-128">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0ef7c-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-129">Select **Create**.</span></span>

1. <span data-ttu-id="0ef7c-130">Wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="0ef7c-131">W obszarze **Zaawansowane**zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0ef7c-132">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ef7c-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ef7c-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0ef7c-134">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0ef7c-135">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0ef7c-136">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0ef7c-137">Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ef7c-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ef7c-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ef7c-139">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="0ef7c-140">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="0ef7c-141">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="0ef7c-142">Wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0ef7c-143">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-143">Select **Next**.</span></span>

1. <span data-ttu-id="0ef7c-144">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="0ef7c-145">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="0ef7c-146">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-146">Select **Next**.</span></span>

1. <span data-ttu-id="0ef7c-147">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="0ef7c-148">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-148">Select **Create**.</span></span>

   <span data-ttu-id="0ef7c-149">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="0ef7c-150">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="0ef7c-151">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).</span><span class="sxs-lookup"><span data-stu-id="0ef7c-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ef7c-152">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ef7c-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0ef7c-153">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="0ef7c-154">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="0ef7c-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ef7c-155">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ef7c-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0ef7c-156">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0ef7c-157">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0ef7c-158">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="0ef7c-159">W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="0ef7c-160">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0ef7c-161">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ef7c-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ef7c-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0ef7c-163">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ef7c-164">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ef7c-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ef7c-165">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0ef7c-166">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="0ef7c-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="0ef7c-167">Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="0ef7c-168">W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu i wybierz polecenie **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="0ef7c-169">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ef7c-170">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ef7c-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0ef7c-171">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="0ef7c-172">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-172">Add a SignalR hub</span></span>

<span data-ttu-id="0ef7c-173">W `BlazorSignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):</span><span class="sxs-lookup"><span data-stu-id="0ef7c-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="0ef7c-174">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="0ef7c-175">W `BlazorSignalRApp.Server` projekcie Otwórz `Startup.cs` plik.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="0ef7c-176">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="0ef7c-177">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0ef7c-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="0ef7c-178">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="0ef7c-179">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="0ef7c-180">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="0ef7c-181">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="0ef7c-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="0ef7c-182">W `BlazorSignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="0ef7c-183">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="0ef7c-184">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ef7c-184">Run the app</span></span>

1. <span data-ttu-id="0ef7c-185">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ef7c-186">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ef7c-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0ef7c-187">W **Eksplorator rozwiązań**wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="0ef7c-188">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ef7c-189">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ef7c-190">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0ef7c-191">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-191">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ef7c-193">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ef7c-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ef7c-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ef7c-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0ef7c-195">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="0ef7c-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="0ef7c-196">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ef7c-197">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ef7c-198">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0ef7c-199">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-199">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ef7c-201">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ef7c-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ef7c-202">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ef7c-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ef7c-203">Na pasku bocznym **rozwiązania** wybierz `BlazorSignalRApp.Server` projekt.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="0ef7c-204">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ef7c-205">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ef7c-206">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0ef7c-207">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-207">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ef7c-209">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ef7c-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ef7c-210">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ef7c-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0ef7c-211">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0ef7c-212">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ef7c-213">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość.</span><span class="sxs-lookup"><span data-stu-id="0ef7c-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="0ef7c-214">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ef7c-216">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ef7c-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0ef7c-217">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="0ef7c-217">Next steps</span></span>

<span data-ttu-id="0ef7c-218">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ef7c-219">Tworzenie Blazor WebAssembly projektu hostowanej aplikacji</span><span class="sxs-lookup"><span data-stu-id="0ef7c-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0ef7c-220">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="0ef7c-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="0ef7c-221">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="0ef7c-222">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="0ef7c-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0ef7c-223">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="0ef7c-223">Add Razor component code for chat</span></span>

<span data-ttu-id="0ef7c-224">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="0ef7c-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="0ef7c-225">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0ef7c-225">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="0ef7c-226">SignalRnegocjowanie między źródłami na potrzeby uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0ef7c-226">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
