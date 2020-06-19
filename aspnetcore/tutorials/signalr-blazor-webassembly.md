---
title: Używanie ASP.NET Core SignalR z Blazor zestawem webassembly
author: guardrex
description: Utwórz aplikację czatu, która używa ASP.NET Core SignalR z Blazor zestawem webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102697"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="6aad2-103">Używanie ASP.NET Core SignalR z Blazor zestawem webassembly</span><span class="sxs-lookup"><span data-stu-id="6aad2-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="6aad2-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6aad2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6aad2-105">W tym samouczku przedstawiono podstawy tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR Blazor zestawu webassembly.</span><span class="sxs-lookup"><span data-stu-id="6aad2-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="6aad2-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="6aad2-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6aad2-107">Tworzenie Blazor projektu hostowanej aplikacji sieci Webassembly</span><span class="sxs-lookup"><span data-stu-id="6aad2-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6aad2-108">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="6aad2-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="6aad2-109">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="6aad2-110">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6aad2-111">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="6aad2-111">Add Razor component code for chat</span></span>

<span data-ttu-id="6aad2-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="6aad2-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6aad2-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6aad2-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6aad2-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6aad2-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6aad2-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6aad2-116">[Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="6aad2-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6aad2-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6aad2-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6aad2-118">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6aad2-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6aad2-119">Visual Studio dla komputerów Mac wersja 8,6 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="6aad2-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6aad2-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6aad2-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="6aad2-121">Utwórz projekt aplikacji hostowanego elementu Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="6aad2-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="6aad2-122">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="6aad2-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6aad2-123">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6aad2-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="6aad2-124">Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="6aad2-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="6aad2-125">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-125">Create a new project.</span></span>

1. <span data-ttu-id="6aad2-126">Wybierz pozycję \*\* Blazor aplikacja\*\* i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="6aad2-127">Wpisz "BlazorSignalRApp" w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="6aad2-128">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6aad2-129">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-129">Select **Create**.</span></span>

1. <span data-ttu-id="6aad2-130">Wybierz szablon \*\* Blazor aplikacji webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="6aad2-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="6aad2-131">W obszarze **Zaawansowane**zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="6aad2-132">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="6aad2-133">Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a Blazor szablon webassembly nie jest wyświetlany w interfejsie użytkownika programu vs, należy ponownie zainstalować szablon przy użyciu `dotnet new` podanego polecenia.</span><span class="sxs-lookup"><span data-stu-id="6aad2-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6aad2-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6aad2-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6aad2-135">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6aad2-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="6aad2-136">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aad2-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="6aad2-137">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="6aad2-138">Visual Studio Code automatycznie dodaje folder *. programu vscode* z wygenerowanym *launch.jsna* plikach i *tasks.js* .</span><span class="sxs-lookup"><span data-stu-id="6aad2-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6aad2-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6aad2-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6aad2-140">Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6aad2-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="6aad2-141">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="6aad2-142">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="6aad2-143">Wybierz szablon \*\* Blazor aplikacji webassembly\*\* .</span><span class="sxs-lookup"><span data-stu-id="6aad2-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6aad2-144">Wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-144">Select **Next**.</span></span>

   <span data-ttu-id="6aad2-145">Potwierdź następujące konfiguracje:</span><span class="sxs-lookup"><span data-stu-id="6aad2-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="6aad2-146">**Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="6aad2-147">**Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="6aad2-148">Zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="6aad2-149">Wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-149">Select **Next**.</span></span>

1. <span data-ttu-id="6aad2-150">W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="6aad2-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="6aad2-151">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-151">Select **Create**.</span></span>

   <span data-ttu-id="6aad2-152">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="6aad2-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="6aad2-153">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="6aad2-154">Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="6aad2-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6aad2-155">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6aad2-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6aad2-156">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6aad2-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="6aad2-157">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="6aad2-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6aad2-158">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6aad2-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="6aad2-159">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6aad2-160">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="6aad2-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="6aad2-161">Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. SignalR . Klient "w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="6aad2-162">W wynikach wyszukiwania wybierz pozycję [Microsoft. AspNetCore. SignalR . Pakiet klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="6aad2-163">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="6aad2-164">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="6aad2-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6aad2-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6aad2-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="6aad2-166">W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="6aad2-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6aad2-167">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6aad2-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6aad2-168">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6aad2-169">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że na liście rozwijanej źródła jest ustawiona wartość *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="6aad2-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="6aad2-170">Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. SignalR . Klient "w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="6aad2-171">W wynikach wyszukiwania zaznacz pole wyboru obok pozycji [Microsoft. AspNetCore. SignalR . Pakiet klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) i wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="6aad2-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="6aad2-172">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="6aad2-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6aad2-173">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6aad2-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6aad2-174">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="6aad2-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="6aad2-175">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-175">Add a SignalR hub</span></span>

<span data-ttu-id="6aad2-176">W projekcie **BlazorSignalRApp. Server** Utwórz folder *Hubs* (plural) i Dodaj następującą `ChatHub` klasę (*Hubs/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="6aad2-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="6aad2-177">Dodaj usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="6aad2-178">W projekcie **BlazorSignalRApp. Server** otwórz plik *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="6aad2-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="6aad2-179">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="6aad2-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="6aad2-180">Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6aad2-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="6aad2-181">W pliku `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6aad2-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="6aad2-182">Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="6aad2-183">Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.</span><span class="sxs-lookup"><span data-stu-id="6aad2-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="6aad2-184">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="6aad2-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="6aad2-185">W projekcie **BlazorSignalRApp. Client** Otwórz plik *Pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="6aad2-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="6aad2-186">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6aad2-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="6aad2-187">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6aad2-187">Run the app</span></span>

1. <span data-ttu-id="6aad2-188">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="6aad2-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6aad2-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6aad2-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6aad2-190">W **Eksplorator rozwiązań**wybierz projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="6aad2-191">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6aad2-192">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6aad2-193">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="6aad2-194">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="6aad2-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6aad2-195">![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6aad2-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6aad2-196">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6aad2-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6aad2-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6aad2-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6aad2-198">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwerowej (*. programu vscode/launch.json*), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestawowi aplikacji ( `{APPLICATION NAME}.Server.dll` ):</span><span class="sxs-lookup"><span data-stu-id="6aad2-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="6aad2-199">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6aad2-200">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6aad2-201">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="6aad2-202">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="6aad2-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6aad2-203">![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6aad2-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6aad2-204">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6aad2-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6aad2-205">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6aad2-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6aad2-206">Na pasku bocznym **rozwiązania** wybierz projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="6aad2-207">Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="6aad2-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6aad2-208">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6aad2-209">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="6aad2-210">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="6aad2-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6aad2-211">![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6aad2-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6aad2-212">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6aad2-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6aad2-213">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="6aad2-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6aad2-214">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="6aad2-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="6aad2-215">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6aad2-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6aad2-216">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="6aad2-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="6aad2-217">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="6aad2-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="6aad2-218">![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6aad2-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="6aad2-219">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6aad2-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="6aad2-220">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="6aad2-220">Next steps</span></span>

<span data-ttu-id="6aad2-221">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="6aad2-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6aad2-222">Tworzenie Blazor projektu hostowanej aplikacji sieci Webassembly</span><span class="sxs-lookup"><span data-stu-id="6aad2-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6aad2-223">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="6aad2-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="6aad2-224">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="6aad2-225">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6aad2-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6aad2-226">Dodawanie Razor kodu składnika dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="6aad2-226">Add Razor component code for chat</span></span>

<span data-ttu-id="6aad2-227">Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="6aad2-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="6aad2-228">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6aad2-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="6aad2-229">[SignalRnegocjowanie między źródłami na potrzeby uwierzytelniania](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="6aad2-229">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
