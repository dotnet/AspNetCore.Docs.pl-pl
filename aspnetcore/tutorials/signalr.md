---
title: 'Wprowadzenie do ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: 'W tym samouczku utworzysz aplikację czatu korzystającą ASP.NET Core :::no-loc(SignalR)::: .'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr
ms.openlocfilehash: 59c296f3388e71254badb02fa3ae4279005c359c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056884"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="ee1ce-103">Samouczek: wprowadzenie do ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ee1ce-103">Tutorial: Get started with ASP.NET Core :::no-loc(SignalR):::</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee1ce-104">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu programu :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-104">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR):::.</span></span> <span data-ttu-id="ee1ce-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ee1ce-106">Utwórz projekt sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-106">Create a web project.</span></span>
> * <span data-ttu-id="ee1ce-107">Dodaj :::no-loc(SignalR)::: bibliotekę kliencką.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-107">Add the :::no-loc(SignalR)::: client library.</span></span>
> * <span data-ttu-id="ee1ce-108">Utwórz :::no-loc(SignalR)::: centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-108">Create a :::no-loc(SignalR)::: hub.</span></span>
> * <span data-ttu-id="ee1ce-109">Skonfiguruj projekt do użycia :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-109">Configure the project to use :::no-loc(SignalR):::.</span></span>
> * <span data-ttu-id="ee1ce-110">Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="ee1ce-111">Na końcu będziesz mieć działającą aplikację czatu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-111">At the end, you'll have a working chat app:</span></span>

![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="ee1ce-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ee1ce-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="ee1ce-117">Tworzenie projektu aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="ee1ce-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ee1ce-119">Z menu wybierz pozycję **plik > nowy projekt** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-119">From the menu, select **File > New Project** .</span></span>

* <span data-ttu-id="ee1ce-120">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** , a następnie wybierz przycisk **dalej** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** , and then select **Next** .</span></span>

* <span data-ttu-id="ee1ce-121">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij *:::no-loc(SignalR)::: rozmowę* projektu, a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-121">In the **Configure your new project** dialog, name the project *:::no-loc(SignalR):::Chat* , and then select **Create** .</span></span>

* <span data-ttu-id="ee1ce-122">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1** .</span></span> 

* <span data-ttu-id="ee1ce-123">Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa :::no-loc(Razor)::: stron, a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-123">Select **Web Application** to create a project that uses :::no-loc(Razor)::: Pages, and then select **Create** .</span></span>

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ee1ce-126">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="ee1ce-127">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o :::no-loc(SignalR):::Chat
   code -r :::no-loc(SignalR):::Chat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-128">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ee1ce-129">Z menu wybierz pozycję **plik > nowe rozwiązanie** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-129">From the menu, select **File > New Solution** .</span></span>

* <span data-ttu-id="ee1ce-130">Wybierz pozycję **.NET Core > App > aplikacji sieci Web** (nie zaznaczaj **aplikacji sieci Web (Model-View-Controller)** ), a następnie wybierz przycisk **dalej** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)** ), and then select **Next** .</span></span>

* <span data-ttu-id="ee1ce-131">Upewnij się, że **platforma docelowa** jest ustawiona na **platformę .NET Core 3,1** , a następnie wybierz przycisk **dalej** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-131">Make sure the **Target Framework** is set to **.NET Core 3.1** , and then select **Next** .</span></span>

* <span data-ttu-id="ee1ce-132">Nadaj nazwę *:::no-loc(SignalR)::: rozmowie* projektu, a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-132">Name the project *:::no-loc(SignalR):::Chat* , and then select **Create** .</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="ee1ce-133">Dodawanie :::no-loc(SignalR)::: biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="ee1ce-133">Add the :::no-loc(SignalR)::: client library</span></span>

<span data-ttu-id="ee1ce-134">:::no-loc(SignalR):::Biblioteka serwerów jest dołączona do struktury udostępnionej ASP.NET Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-134">The :::no-loc(SignalR)::: server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="ee1ce-135">Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="ee1ce-136">W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg* .</span></span> <span data-ttu-id="ee1ce-137">unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w npm, Menedżer pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ee1ce-139">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-139">In **Solution Explorer** , right-click the project, and select **Add** > **Client-Side Library** .</span></span>

* <span data-ttu-id="ee1ce-140">W oknie dialogowym **Dodaj bibliotekę Client-Side** , dla **dostawcy** wybierz pozycję **unpkg** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg** .</span></span>

* <span data-ttu-id="ee1ce-141">W obszarze **Biblioteka** wprowadź `@microsoft/signalr@latest` .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-141">For **Library** , enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="ee1ce-142">Wybierz pozycję **Wybierz określone pliki** , rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *signalr.js* i *signalr.min.js* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-142">Select **Choose specific files** , expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js* .</span></span>

* <span data-ttu-id="ee1ce-143">Ustaw **lokalizację docelową** na plik *wwwroot/js/signaler/* , a następnie wybierz pozycję **Zainstaluj** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-143">Set **Target Location** to *wwwroot/js/signalr/* , and select **Install** .</span></span>

  ![Okno dialogowe Dodawanie biblioteki Client-Side — wybór biblioteki](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="ee1ce-145">LibMan tworzy folder *wwwroot/js/sygnalizujący* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ee1ce-147">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ee1ce-148">Uruchom następujące polecenie, aby pobrać :::no-loc(SignalR)::: bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-148">Run the following command to get the :::no-loc(SignalR)::: client library by using LibMan.</span></span> <span data-ttu-id="ee1ce-149">Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ee1ce-150">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ee1ce-151">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ee1ce-152">Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="ee1ce-153">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-153">Copy only the specified files.</span></span>

  <span data-ttu-id="ee1ce-154">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-155">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ee1ce-156">W **terminalu** Uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-156">In the **Terminal** , run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ee1ce-157">Przejdź do folderu projektu (taki, który zawiera plik *:::no-loc(SignalR)::: Chat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="ee1ce-157">Navigate to the project folder (the one that contains the *:::no-loc(SignalR):::Chat.csproj* file).</span></span>

* <span data-ttu-id="ee1ce-158">Uruchom następujące polecenie, aby pobrać :::no-loc(SignalR)::: bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-158">Run the following command to get the :::no-loc(SignalR)::: client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ee1ce-159">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ee1ce-160">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ee1ce-161">Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="ee1ce-162">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-162">Copy only the specified files.</span></span>

  <span data-ttu-id="ee1ce-163">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="ee1ce-164">Tworzenie :::no-loc(SignalR)::: centrum</span><span class="sxs-lookup"><span data-stu-id="ee1ce-164">Create a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="ee1ce-165">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="ee1ce-166">W :::no-loc(SignalR)::: folderze projektu rozmowy utwórz folder *Hubs* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-166">In the :::no-loc(SignalR):::Chat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="ee1ce-167">W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="ee1ce-168">`ChatHub`Klasa dziedziczy z :::no-loc(SignalR)::: `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-168">The `ChatHub` class inherits from the :::no-loc(SignalR)::: `Hub` class.</span></span> <span data-ttu-id="ee1ce-169">`Hub`Klasa zarządza połączeniami, grupami i obsługą wiadomości.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="ee1ce-170">`SendMessage`Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="ee1ce-171">Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="ee1ce-172">:::no-loc(SignalR)::: kod jest asynchroniczny, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-172">:::no-loc(SignalR)::: code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="ee1ce-173">Ponowne :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ee1ce-173">Configure :::no-loc(SignalR):::</span></span>

<span data-ttu-id="ee1ce-174">:::no-loc(SignalR):::Serwer musi być skonfigurowany tak, aby przekazywać :::no-loc(SignalR)::: żądania do :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-174">The :::no-loc(SignalR)::: server must be configured to pass :::no-loc(SignalR)::: requests to :::no-loc(SignalR):::.</span></span>

* <span data-ttu-id="ee1ce-175">Dodaj następujący wyróżniony kod do pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="ee1ce-176">Te zmiany są dodawane :::no-loc(SignalR)::: do ASP.NET Core systemów iniekcji i routingu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-176">These changes add :::no-loc(SignalR)::: to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="ee1ce-177">Dodawanie :::no-loc(SignalR)::: kodu klienta</span><span class="sxs-lookup"><span data-stu-id="ee1ce-177">Add :::no-loc(SignalR)::: client code</span></span>

* <span data-ttu-id="ee1ce-178">Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="ee1ce-179">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-179">The preceding code:</span></span>

  * <span data-ttu-id="ee1ce-180">Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="ee1ce-181">Tworzy listę `id="messagesList"` na potrzeby wyświetlania komunikatów odbieranych z :::no-loc(SignalR)::: centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-181">Creates a list with `id="messagesList"` for displaying messages that are received from the :::no-loc(SignalR)::: hub.</span></span>
  * <span data-ttu-id="ee1ce-182">Zawiera odwołania do skryptu do :::no-loc(SignalR)::: i kod aplikacji *chat.js* utworzony w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-182">Includes script references to :::no-loc(SignalR)::: and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="ee1ce-183">W folderze *wwwroot/js* utwórz plik *chat.js* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="ee1ce-184">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-184">The preceding code:</span></span>

  * <span data-ttu-id="ee1ce-185">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="ee1ce-186">Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="ee1ce-187">Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ee1ce-188">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ee1ce-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ee1ce-190">Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ee1ce-192">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p :::no-loc(SignalR):::Chat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-193">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ee1ce-194">Z menu wybierz polecenie **uruchom > Uruchom bez debugowania** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-194">From the menu, select **Run > Start Without Debugging** .</span></span>

---

* <span data-ttu-id="ee1ce-195">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="ee1ce-196">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="ee1ce-197">Nazwa i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-197">The name and message are displayed on both pages instantly.</span></span>

  ![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="ee1ce-199">Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="ee1ce-200">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="ee1ce-201">Załóżmy na przykład, że należy umieścić *signalr.js* w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="ee1ce-202">W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="ee1ce-203">![ Błąd nie znalezionosignalr.js](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="ee1ce-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="ee1ce-204">Jeśli zostanie wyświetlony komunikat o błędzie ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY w programie Chrome, uruchom następujące polecenia, aby zaktualizować certyfikat deweloperski:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ee1ce-205">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu programu :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-205">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR):::.</span></span> <span data-ttu-id="ee1ce-206">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="ee1ce-207">Utwórz projekt sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-207">Create a web project.</span></span>   
> * <span data-ttu-id="ee1ce-208">Dodaj :::no-loc(SignalR)::: bibliotekę kliencką.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-208">Add the :::no-loc(SignalR)::: client library.</span></span>   
> * <span data-ttu-id="ee1ce-209">Utwórz :::no-loc(SignalR)::: centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-209">Create a :::no-loc(SignalR)::: hub.</span></span> 
> * <span data-ttu-id="ee1ce-210">Skonfiguruj projekt do użycia :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-210">Configure the project to use :::no-loc(SignalR):::.</span></span> 
> * <span data-ttu-id="ee1ce-211">Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="ee1ce-212">Na końcu będziesz mieć działającą aplikację czatu:::: ![ no-Loc (signaler)::: przykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="ee1ce-212">At the end, you'll have a working chat app: ![:::no-loc(SignalR)::: sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="ee1ce-213">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ee1ce-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-216">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="ee1ce-217">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="ee1ce-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="ee1ce-219">Z menu wybierz pozycję **plik > nowy projekt** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-219">From the menu, select **File > New Project** .</span></span> 

* <span data-ttu-id="ee1ce-220">W oknie dialogowym **Nowy projekt** wybierz pozycję **zainstalowane > Visual C# > sieci Web > ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application** .</span></span> <span data-ttu-id="ee1ce-221">Nadaj nazwę *:::no-loc(SignalR)::: rozmowie* projektu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-221">Name the project *:::no-loc(SignalR):::Chat* .</span></span>   

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="ee1ce-223">Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa :::no-loc(Razor)::: stron.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-223">Select **Web Application** to create a project that uses :::no-loc(Razor)::: Pages.</span></span>   

* <span data-ttu-id="ee1ce-224">Wybierz platformę docelową programu **.NET Core** , wybierz pozycję **ASP.NET Core 2,2** , a następnie kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-224">Select a target framework of **.NET Core** , select **ASP.NET Core 2.2** , and click **OK** .</span></span>    

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="ee1ce-227">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="ee1ce-228">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o :::no-loc(SignalR):::Chat   
   code -r :::no-loc(SignalR):::Chat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-229">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="ee1ce-230">Z menu wybierz pozycję **plik > nowe rozwiązanie** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-230">From the menu, select **File > New Solution** .</span></span>    

* <span data-ttu-id="ee1ce-231">Wybierz pozycję **.NET Core > app > ASP.NET Core Web App** (nie wybieraj **ASP.NET Core Web App (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="ee1ce-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)** ).</span></span>  

* <span data-ttu-id="ee1ce-232">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-232">Select **Next** .</span></span>  

* <span data-ttu-id="ee1ce-233">Nadaj nazwę *:::no-loc(SignalR)::: rozmowie* projektu, a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-233">Name the project *:::no-loc(SignalR):::Chat* , and then select **Create** .</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="ee1ce-234">Dodawanie :::no-loc(SignalR)::: biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="ee1ce-234">Add the :::no-loc(SignalR)::: client library</span></span> 

<span data-ttu-id="ee1ce-235">:::no-loc(SignalR):::Biblioteka serwerów jest dołączona do `Microsoft.AspNetCore.App` pakietu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-235">The :::no-loc(SignalR)::: server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="ee1ce-236">Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="ee1ce-237">W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg* .</span></span> <span data-ttu-id="ee1ce-238">unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w npm, Menedżer pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="ee1ce-240">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-240">In **Solution Explorer** , right-click the project, and select **Add** > **Client-Side Library** .</span></span>  

* <span data-ttu-id="ee1ce-241">W oknie dialogowym **Dodaj bibliotekę Client-Side** , dla **dostawcy** wybierz pozycję **unpkg** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg** .</span></span> 

* <span data-ttu-id="ee1ce-242">W polu **Biblioteka** wprowadź `@microsoft/signalr@3` i wybierz najnowszą wersję, która nie jest w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-242">For **Library** , enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Okno dialogowe Dodawanie biblioteki Client-Side — wybór biblioteki](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="ee1ce-244">Wybierz pozycję **Wybierz określone pliki** , rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *signalr.js* i *signalr.min.js* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-244">Select **Choose specific files** , expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js* .</span></span> 

* <span data-ttu-id="ee1ce-245">Ustaw **lokalizację docelową** na plik *wwwroot/lib/sygnalizujący/* , a następnie wybierz pozycję **Zainstaluj** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-245">Set **Target Location** to *wwwroot/lib/signalr/* , and select **Install** .</span></span>    

  ![Okno dialogowe Dodawanie Client-Side biblioteki — Wybieranie plików i lokalizacji docelowej](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="ee1ce-247">LibMan tworzy folder *wwwroot/lib/sygnalizujący* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="ee1ce-249">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="ee1ce-250">Uruchom następujące polecenie, aby pobrać :::no-loc(SignalR)::: bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-250">Run the following command to get the :::no-loc(SignalR)::: client library by using LibMan.</span></span> <span data-ttu-id="ee1ce-251">Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="ee1ce-252">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="ee1ce-253">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="ee1ce-254">Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="ee1ce-255">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="ee1ce-256">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-257">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="ee1ce-258">W **terminalu** Uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-258">In the **Terminal** , run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="ee1ce-259">Przejdź do folderu projektu (taki, który zawiera plik *:::no-loc(SignalR)::: Chat. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="ee1ce-259">Navigate to the project folder (the one that contains the *:::no-loc(SignalR):::Chat.csproj* file).</span></span>   

* <span data-ttu-id="ee1ce-260">Uruchom następujące polecenie, aby pobrać :::no-loc(SignalR)::: bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-260">Run the following command to get the :::no-loc(SignalR)::: client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="ee1ce-261">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="ee1ce-262">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="ee1ce-263">Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="ee1ce-264">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="ee1ce-265">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="ee1ce-266">Tworzenie :::no-loc(SignalR)::: centrum</span><span class="sxs-lookup"><span data-stu-id="ee1ce-266">Create a :::no-loc(SignalR)::: hub</span></span>   

<span data-ttu-id="ee1ce-267">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="ee1ce-268">W :::no-loc(SignalR)::: folderze projektu rozmowy utwórz folder *Hubs* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-268">In the :::no-loc(SignalR):::Chat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="ee1ce-269">W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="ee1ce-270">`ChatHub`Klasa dziedziczy z :::no-loc(SignalR)::: `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-270">The `ChatHub` class inherits from the :::no-loc(SignalR)::: `Hub` class.</span></span> <span data-ttu-id="ee1ce-271">`Hub`Klasa zarządza połączeniami, grupami i obsługą wiadomości.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="ee1ce-272">`SendMessage`Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="ee1ce-273">Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="ee1ce-274">:::no-loc(SignalR)::: kod jest asynchroniczny, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-274">:::no-loc(SignalR)::: code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="ee1ce-275">Ponowne :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ee1ce-275">Configure :::no-loc(SignalR):::</span></span>  

<span data-ttu-id="ee1ce-276">:::no-loc(SignalR):::Serwer musi być skonfigurowany tak, aby przekazywać :::no-loc(SignalR)::: żądania do :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-276">The :::no-loc(SignalR)::: server must be configured to pass :::no-loc(SignalR)::: requests to :::no-loc(SignalR):::.</span></span>    

* <span data-ttu-id="ee1ce-277">Dodaj następujący wyróżniony kod do pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="ee1ce-278">Te zmiany są dodawane :::no-loc(SignalR)::: do ASP.NET Core systemu iniekcji zależności oraz potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-278">These changes add :::no-loc(SignalR)::: to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="ee1ce-279">Dodawanie :::no-loc(SignalR)::: kodu klienta</span><span class="sxs-lookup"><span data-stu-id="ee1ce-279">Add :::no-loc(SignalR)::: client code</span></span>    

* <span data-ttu-id="ee1ce-280">Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="ee1ce-281">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-281">The preceding code:</span></span>   

  * <span data-ttu-id="ee1ce-282">Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="ee1ce-283">Tworzy listę `id="messagesList"` na potrzeby wyświetlania komunikatów odbieranych z :::no-loc(SignalR)::: centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-283">Creates a list with `id="messagesList"` for displaying messages that are received from the :::no-loc(SignalR)::: hub.</span></span>   
  * <span data-ttu-id="ee1ce-284">Zawiera odwołania do skryptu do :::no-loc(SignalR)::: i kod aplikacji *chat.js* utworzony w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-284">Includes script references to :::no-loc(SignalR)::: and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="ee1ce-285">W folderze *wwwroot/js* utwórz plik *chat.js* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="ee1ce-286">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-286">The preceding code:</span></span>   

  * <span data-ttu-id="ee1ce-287">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="ee1ce-288">Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="ee1ce-289">Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="ee1ce-290">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ee1ce-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="ee1ce-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ee1ce-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="ee1ce-292">Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="ee1ce-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ee1ce-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="ee1ce-294">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="ee1ce-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p :::no-loc(SignalR):::Chat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ee1ce-295">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ee1ce-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ee1ce-296">Z menu wybierz polecenie **uruchom > Uruchom bez debugowania** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-296">From the menu, select **Run > Start Without Debugging** .</span></span>

---

* <span data-ttu-id="ee1ce-297">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="ee1ce-298">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .</span><span class="sxs-lookup"><span data-stu-id="ee1ce-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="ee1ce-299">Nazwa i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-299">The name and message are displayed on both pages instantly.</span></span>   

  ![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="ee1ce-301">Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="ee1ce-302">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="ee1ce-303">Załóżmy na przykład, że należy umieścić *signalr.js* w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="ee1ce-304">W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="ee1ce-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="ee1ce-305">![ Błąd nie znalezionosignalr.js](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="ee1ce-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="ee1ce-306">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ee1ce-306">Additional resources</span></span> 
* [<span data-ttu-id="ee1ce-307">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="ee1ce-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
