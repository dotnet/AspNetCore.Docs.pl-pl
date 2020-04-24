---
title: Używanie ASP.NET Core SignalR z Blazor zestawem webassembly
author: guardrex
description: Utwórz aplikację czatu, która używa ASP.NET Core SignalR z Blazor zestawem webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 78c5fbb8b91b934bcb34525672e9e26b6a95290e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111152"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="536a2-103">Korzystanie z ASP.NET Core sygnalizującego z zestawem webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="536a2-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="536a2-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="536a2-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="536a2-105">W tym samouczku przedstawiono podstawowe informacje na temat tworzenia aplikacji w czasie rzeczywistym przy użyciu usługi sygnalizującej z zestawem webBlazor.</span><span class="sxs-lookup"><span data-stu-id="536a2-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="536a2-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="536a2-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="536a2-107">Tworzenie projektu hostowanej aplikacji sieci webassembly Blazor</span><span class="sxs-lookup"><span data-stu-id="536a2-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="536a2-108">Dodawanie biblioteki klienta sygnalizującego</span><span class="sxs-lookup"><span data-stu-id="536a2-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="536a2-109">Dodawanie centrum sygnałów</span><span class="sxs-lookup"><span data-stu-id="536a2-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="536a2-110">Dodaj usługi sygnalizujące i punkt końcowy centrum sygnałów</span><span class="sxs-lookup"><span data-stu-id="536a2-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="536a2-111">Dodawanie kodu składnika Razor dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="536a2-111">Add Razor component code for chat</span></span>

<span data-ttu-id="536a2-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="536a2-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="536a2-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="536a2-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="536a2-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="536a2-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="536a2-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="536a2-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="536a2-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="536a2-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="536a2-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="536a2-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="536a2-118">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="536a2-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="536a2-119">Utwórz projekt aplikacji hostowanej Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="536a2-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="536a2-120">Gdy nie korzystasz z programu Visual Studio w wersji 16,6 Preview 2 lub nowszej, zainstaluj szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) .</span><span class="sxs-lookup"><span data-stu-id="536a2-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="536a2-121">Pakiet [Microsoft. AspNetCore. Components. webassembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ma wersję zapoznawczą, a Blazor webassembly jest w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="536a2-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="536a2-122">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="536a2-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
```

<span data-ttu-id="536a2-123">Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:</span><span class="sxs-lookup"><span data-stu-id="536a2-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="536a2-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="536a2-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="536a2-125">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="536a2-125">Create a new project.</span></span>

1. <span data-ttu-id="536a2-126">Wybierz pozycję **aplikacja Blazor** i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="536a2-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="536a2-127">Wpisz "BlazorSignalRApp" w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="536a2-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="536a2-128">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="536a2-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="536a2-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="536a2-129">Select **Create**.</span></span>

1. <span data-ttu-id="536a2-130">Wybierz szablon **aplikacji Webassembly Blazor** .</span><span class="sxs-lookup"><span data-stu-id="536a2-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="536a2-131">W obszarze **Zaawansowane**zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="536a2-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="536a2-132">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="536a2-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="536a2-133">Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a szablon Blazor webassembly nie jest wyświetlany w interfejsie użytkownika programu VS, należy `dotnet new` ponownie zainstalować szablon przy użyciu podanego wcześniej polecenia.</span><span class="sxs-lookup"><span data-stu-id="536a2-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="536a2-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="536a2-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="536a2-135">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="536a2-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="536a2-136">W Visual Studio Code Otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="536a2-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="536a2-137">Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="536a2-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="536a2-138">Visual Studio Code automatycznie dodaje folder *. programu vscode* z wygenerowanymi plikami *Launch. JSON* i *Tasks. JSON* .</span><span class="sxs-lookup"><span data-stu-id="536a2-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="536a2-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="536a2-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="536a2-140">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="536a2-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="536a2-141">W Visual Studio dla komputerów Mac otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*. sln*).</span><span class="sxs-lookup"><span data-stu-id="536a2-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="536a2-142">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="536a2-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="536a2-143">W powłoce poleceń wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="536a2-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="536a2-144">Dodawanie biblioteki klienta sygnalizującego</span><span class="sxs-lookup"><span data-stu-id="536a2-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="536a2-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="536a2-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="536a2-146">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="536a2-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="536a2-147">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="536a2-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="536a2-148">Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. signaler. Client" w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="536a2-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="536a2-149">W wynikach wyszukiwania wybierz `Microsoft.AspNetCore.SignalR.Client` pakiet i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="536a2-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="536a2-150">Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="536a2-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="536a2-151">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="536a2-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="536a2-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="536a2-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="536a2-153">W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="536a2-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="536a2-154">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="536a2-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="536a2-155">Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="536a2-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="536a2-156">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że na liście rozwijanej źródła jest ustawiona wartość *NuGet.org*.</span><span class="sxs-lookup"><span data-stu-id="536a2-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="536a2-157">Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. signaler. Client" w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="536a2-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="536a2-158">W wynikach wyszukiwania zaznacz pole wyboru obok `Microsoft.AspNetCore.SignalR.Client` pakietu i wybierz polecenie **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="536a2-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="536a2-159">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.</span><span class="sxs-lookup"><span data-stu-id="536a2-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="536a2-160">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="536a2-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="536a2-161">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="536a2-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="536a2-162">Dodawanie centrum sygnałów</span><span class="sxs-lookup"><span data-stu-id="536a2-162">Add a SignalR hub</span></span>

<span data-ttu-id="536a2-163">W projekcie **BlazorSignalRApp. Server** Utwórz folder *Hubs* (plural) i Dodaj następującą `ChatHub` klasę (*Hubs/ChatHub. cs*):</span><span class="sxs-lookup"><span data-stu-id="536a2-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="536a2-164">Dodaj usługi i punkt końcowy centrum sygnałów</span><span class="sxs-lookup"><span data-stu-id="536a2-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="536a2-165">W projekcie **BlazorSignalRApp. Server** otwórz plik *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="536a2-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="536a2-166">Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:</span><span class="sxs-lookup"><span data-stu-id="536a2-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="536a2-167">Dodaj sygnały sygnalizujące i odpowiedzi na kompresję usług pośredniczących do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="536a2-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="536a2-168">W `Startup.Configure` obszarze punkty końcowe dla kontrolerów i powrotu po stronie klienta Dodaj punkt końcowy centrum:</span><span class="sxs-lookup"><span data-stu-id="536a2-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="536a2-169">Dodawanie kodu składnika Razor dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="536a2-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="536a2-170">W projekcie **BlazorSignalRApp. Client** Otwórz plik *Pages/index. Razor* .</span><span class="sxs-lookup"><span data-stu-id="536a2-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="536a2-171">Zastąp znacznik następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="536a2-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="536a2-172">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="536a2-172">Run the app</span></span>

1. <span data-ttu-id="536a2-173">Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:</span><span class="sxs-lookup"><span data-stu-id="536a2-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="536a2-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="536a2-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="536a2-175">W **Eksplorator rozwiązań**wybierz projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="536a2-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="536a2-176">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="536a2-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="536a2-177">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="536a2-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="536a2-178">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="536a2-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="536a2-179">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="536a2-179">The name and message are displayed on both pages instantly:</span></span>

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="536a2-181">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="536a2-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="536a2-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="536a2-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="536a2-183">Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwerowej (*. programu vscode/Launch. JSON*), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestawowi aplikacji (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="536a2-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="536a2-184">Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="536a2-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="536a2-185">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="536a2-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="536a2-186">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="536a2-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="536a2-187">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="536a2-187">The name and message are displayed on both pages instantly:</span></span>

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="536a2-189">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="536a2-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="536a2-190">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="536a2-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="536a2-191">Na pasku bocznym **rozwiązania** wybierz projekt **BlazorSignalRApp. Server** .</span><span class="sxs-lookup"><span data-stu-id="536a2-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="536a2-192">Naciśnij <kbd>⌘</kbd>+<kbd>↩</kbd>\* \*, aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="536a2-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="536a2-193">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="536a2-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="536a2-194">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="536a2-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="536a2-195">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="536a2-195">The name and message are displayed on both pages instantly:</span></span>

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="536a2-197">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="536a2-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="536a2-198">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="536a2-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="536a2-199">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="536a2-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="536a2-200">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="536a2-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="536a2-201">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="536a2-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="536a2-202">Nazwa i komunikat są wyświetlane na obu stronach natychmiast:</span><span class="sxs-lookup"><span data-stu-id="536a2-202">The name and message are displayed on both pages instantly:</span></span>

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="536a2-204">Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="536a2-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="536a2-205">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="536a2-205">Next steps</span></span>

<span data-ttu-id="536a2-206">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="536a2-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="536a2-207">Tworzenie projektu Blazor hostowanej aplikacji sieci webassembly</span><span class="sxs-lookup"><span data-stu-id="536a2-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="536a2-208">Dodawanie biblioteki SignalR klienta</span><span class="sxs-lookup"><span data-stu-id="536a2-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="536a2-209">Dodawanie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="536a2-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="536a2-210">Dodaj SignalR usługi i punkt końcowy SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="536a2-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="536a2-211">Dodawanie kodu składnika Razor dla rozmowy</span><span class="sxs-lookup"><span data-stu-id="536a2-211">Add Razor component code for chat</span></span>

<span data-ttu-id="536a2-212">Aby dowiedzieć się więcej Blazor na temat tworzenia aplikacji Blazor , zapoznaj się z dokumentacją:</span><span class="sxs-lookup"><span data-stu-id="536a2-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="536a2-213">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="536a2-213">Additional resources</span></span>

* <xref:signalr/introduction>
