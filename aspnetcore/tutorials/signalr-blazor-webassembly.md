---
title: Używanie ASP.NET Core SignalR Blazor z webassembly
author: guardrex
description: Utwórz aplikację do czatu, która SignalR Blazor używa ASP.NET Core z WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 798068c83e16070d3279c88c44af0cd96d182fe2
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488887"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="eeecb-103">Użyj ASP.NET Core SignalR z Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="eeecb-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="eeecb-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eeecb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="eeecb-105">Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR z Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="eeecb-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="eeecb-106">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="eeecb-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eeecb-107">Tworzenie projektu aplikacji Hostowane w usłudze Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="eeecb-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="eeecb-108">Dodawanie biblioteki klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="eeecb-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="eeecb-109">Dodawanie koncentratora signalr</span><span class="sxs-lookup"><span data-stu-id="eeecb-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="eeecb-110">Dodawanie usług SignalR i punktu końcowego dla centrum SignalR</span><span class="sxs-lookup"><span data-stu-id="eeecb-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="eeecb-111">Dodawanie kodu komponentu Razor do czatu</span><span class="sxs-lookup"><span data-stu-id="eeecb-111">Add Razor component code for chat</span></span>

<span data-ttu-id="eeecb-112">Na końcu tego samouczka będziesz mieć działającą aplikację czatu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="eeecb-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eeecb-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eeecb-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="eeecb-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eeecb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eeecb-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eeecb-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eeecb-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eeecb-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="eeecb-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="eeecb-118">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="eeecb-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="eeecb-119">Tworzenie hostowanego projektu aplikacji Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="eeecb-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="eeecb-120">Jeśli nie używasz programu Visual Studio w wersji 16.6 Preview 2 lub nowszej, zainstaluj szablon [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="eeecb-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="eeecb-121">Pakiet [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ma wersję zapoznawczą, podczas gdy Blazor WebAssembly jest w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="eeecb-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="eeecb-122">W powłoce polecenia wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="eeecb-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

<span data-ttu-id="eeecb-123">Postępuj zgodnie ze wskazówkami dotyczącymi wyboru narzędzi:</span><span class="sxs-lookup"><span data-stu-id="eeecb-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eeecb-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eeecb-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eeecb-125">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-125">Create a new project.</span></span>

1. <span data-ttu-id="eeecb-126">Wybierz **aplikację Blazor** i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="eeecb-127">Wpisz "BlazorSignalRApp" w polu **Nazwa projektu.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="eeecb-128">Upewnij się, że wpis **Lokalizacja** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="eeecb-129">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-129">Select **Create**.</span></span>

1. <span data-ttu-id="eeecb-130">Wybierz szablon **aplikacji Blazor WebAssembly.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="eeecb-131">W **obszarze Zaawansowane**zaznacz pole wyboru **Hostowany ASP.NET Core.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="eeecb-132">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="eeecb-133">Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a szablon Blazor WebAssembly nie pojawi się `dotnet new` w interfejsie użytkownika programu VS, zainstaluj ponownie szablon przy użyciu polecenia pokazanego wcześniej.</span><span class="sxs-lookup"><span data-stu-id="eeecb-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eeecb-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eeecb-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="eeecb-135">W powłoce polecenia wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="eeecb-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="eeecb-136">W programie Visual Studio Code otwórz folder projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eeecb-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="eeecb-137">Gdy pojawi się okno dialogowe, aby dodać zasoby do tworzenia i debugowania aplikacji, wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="eeecb-138">Program Visual Studio Code automatycznie dodaje folder *vscode* z wygenerowanymi plikami *launch.json* i *tasks.json.*</span><span class="sxs-lookup"><span data-stu-id="eeecb-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eeecb-139">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="eeecb-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eeecb-140">W powłoce polecenia wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="eeecb-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="eeecb-141">W programie Visual Studio dla komputerów Mac otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*.sln*).</span><span class="sxs-lookup"><span data-stu-id="eeecb-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eeecb-142">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="eeecb-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="eeecb-143">W powłoce polecenia wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="eeecb-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="eeecb-144">Dodawanie biblioteki klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="eeecb-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eeecb-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eeecb-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="eeecb-146">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp.Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="eeecb-147">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **źródło pakietu** jest ustawione na *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="eeecb-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="eeecb-148">Po **wybraniu opcji Przeglądaj** wpisz "Microsoft.AspNetCore.SignalR.Client" w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="eeecb-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="eeecb-149">W wynikach wyszukiwania `Microsoft.AspNetCore.SignalR.Client` wybierz pakiet i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="eeecb-150">Jeśli zostanie wyświetlone okno dialogowe **Zmiany podglądu,** wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="eeecb-151">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji,** wybierz **pozycję Akceptuję,** jeśli zgadzasz się z postanowieniami licencyjnymi.</span><span class="sxs-lookup"><span data-stu-id="eeecb-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eeecb-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eeecb-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="eeecb-153">W **terminalu zintegrowanym** **(Widok** > **terminala** z paska narzędzi) wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeecb-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eeecb-154">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="eeecb-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eeecb-155">Na **pasku** bocznym Rozwiązanie kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp.Client** i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="eeecb-156">W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że źródło listy rozwijanej jest ustawiona *na nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="eeecb-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="eeecb-157">Po **wybraniu opcji Przeglądaj** wpisz "Microsoft.AspNetCore.SignalR.Client" w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="eeecb-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="eeecb-158">W wynikach wyszukiwania zaznacz pole wyboru `Microsoft.AspNetCore.SignalR.Client` obok pakietu i wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="eeecb-159">Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji,** wybierz pozycję **Zaakceptuj,** jeśli zgadzasz się z postanowieniami licencyjnymi.</span><span class="sxs-lookup"><span data-stu-id="eeecb-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eeecb-160">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="eeecb-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="eeecb-161">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeecb-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="eeecb-162">Dodawanie koncentratora signalr</span><span class="sxs-lookup"><span data-stu-id="eeecb-162">Add a SignalR hub</span></span>

<span data-ttu-id="eeecb-163">W projekcie **BlazorSignalRApp.Server** utwórz folder *Koncentratorów* (liczba `ChatHub` mnoga) i dodaj następującą klasę (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="eeecb-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="eeecb-164">Dodawanie usług SignalR i punktu końcowego dla centrum SignalR</span><span class="sxs-lookup"><span data-stu-id="eeecb-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="eeecb-165">W projekcie **BlazorSignalRApp.Server** otwórz plik *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="eeecb-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="eeecb-166">Dodaj obszar nazw `ChatHub` dla klasy do górnej części pliku:</span><span class="sxs-lookup"><span data-stu-id="eeecb-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="eeecb-167">Dodaj usługi SignalR `Startup.ConfigureServices`do:</span><span class="sxs-lookup"><span data-stu-id="eeecb-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="eeecb-168">Pomiędzy `Startup.Configure` punktami końcowymi dla domyślnej trasy kontrolera a rezerwowym po stronie klienta dodaj punkt końcowy dla koncentratora:</span><span class="sxs-lookup"><span data-stu-id="eeecb-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="eeecb-169">Dodawanie kodu komponentu Razor do czatu</span><span class="sxs-lookup"><span data-stu-id="eeecb-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="eeecb-170">W projekcie **BlazorSignalRApp.Client** otwórz plik *Pages/Index.razor.*</span><span class="sxs-lookup"><span data-stu-id="eeecb-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="eeecb-171">Zastąp znaczniki następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eeecb-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="eeecb-172">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="eeecb-172">Run the app</span></span>

1. <span data-ttu-id="eeecb-173">Postępuj zgodnie ze wskazówkami dotyczącymi oprzyrządowania:</span><span class="sxs-lookup"><span data-stu-id="eeecb-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eeecb-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eeecb-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eeecb-175">W **Eksploratorze rozwiązań**wybierz projekt **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="eeecb-176">Naciśnij **klawisze Ctrl+F5,** aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="eeecb-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="eeecb-177">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eeecb-178">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="eeecb-179">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:</span><span class="sxs-lookup"><span data-stu-id="eeecb-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="eeecb-181">Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="eeecb-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eeecb-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eeecb-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="eeecb-183">Wybierz **polecenie Uruchom debugowanie** > **bez debugowania** z paska narzędzi.</span><span class="sxs-lookup"><span data-stu-id="eeecb-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="eeecb-184">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eeecb-185">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="eeecb-186">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:</span><span class="sxs-lookup"><span data-stu-id="eeecb-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="eeecb-188">Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="eeecb-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eeecb-189">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="eeecb-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eeecb-190">Na **pasku** bocznym rozwiązanie wybierz projekt **BlazorSignalRApp.Server.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="eeecb-191">Z menu wybierz **polecenie Uruchom** > **start bez debugowania**.</span><span class="sxs-lookup"><span data-stu-id="eeecb-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="eeecb-192">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eeecb-193">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="eeecb-194">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:</span><span class="sxs-lookup"><span data-stu-id="eeecb-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="eeecb-196">Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="eeecb-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eeecb-197">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="eeecb-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="eeecb-198">W powłoce poleceń wykonaj następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="eeecb-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="eeecb-199">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eeecb-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eeecb-200">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eeecb-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="eeecb-201">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:</span><span class="sxs-lookup"><span data-stu-id="eeecb-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="eeecb-203">Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="eeecb-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="eeecb-204">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="eeecb-204">Next steps</span></span>

<span data-ttu-id="eeecb-205">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="eeecb-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eeecb-206">Tworzenie Blazor projektu aplikacji hostowanego przez webassembly</span><span class="sxs-lookup"><span data-stu-id="eeecb-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="eeecb-207">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="eeecb-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="eeecb-208">Dodawanie SignalR koncentratora</span><span class="sxs-lookup"><span data-stu-id="eeecb-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="eeecb-209">Dodawanie SignalR usług i punktu SignalR końcowego dla koncentratora</span><span class="sxs-lookup"><span data-stu-id="eeecb-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="eeecb-210">Dodawanie kodu komponentu Razor do czatu</span><span class="sxs-lookup"><span data-stu-id="eeecb-210">Add Razor component code for chat</span></span>

<span data-ttu-id="eeecb-211">Aby dowiedzieć Blazor się więcej Blazor o tworzeniu aplikacji, zobacz dokumentację:</span><span class="sxs-lookup"><span data-stu-id="eeecb-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="eeecb-212">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="eeecb-212">Additional resources</span></span>

* <xref:signalr/introduction>
