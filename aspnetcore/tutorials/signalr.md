---
title: Wprowadzenie do ASP.NET Core SignalR
author: bradygaster
description: W tym samouczku utworzysz aplikację czatu korzystającą ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
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
uid: tutorials/signalr
ms.openlocfilehash: b69d60e7d0e24f6d3c8032b391c98a6cd1589305
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770184"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="6ddb5-103">Samouczek: wprowadzenie do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="6ddb5-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ddb5-104">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu programu SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="6ddb5-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6ddb5-106">Utwórz projekt sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-106">Create a web project.</span></span>
> * <span data-ttu-id="6ddb5-107">Dodaj SignalR bibliotekę kliencką.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="6ddb5-108">Utwórz SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="6ddb5-109">Skonfiguruj projekt do użycia SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="6ddb5-110">Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="6ddb5-111">Na końcu będziesz mieć działającą aplikację czatu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-111">At the end, you'll have a working chat app:</span></span>

![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="6ddb5-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6ddb5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="6ddb5-117">Tworzenie projektu aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="6ddb5-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="6ddb5-119">Z menu wybierz pozycję **plik > nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="6ddb5-120">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web**, a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="6ddb5-121">W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij \* SignalR rozmowę\*projektu, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="6ddb5-122">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **.net Core** i **ASP.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span> 

* <span data-ttu-id="6ddb5-123">Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa Razor stron, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="6ddb5-126">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="6ddb5-127">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-128">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6ddb5-129">Z menu wybierz pozycję **plik > nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="6ddb5-130">Wybierz pozycję **.NET Core > App > aplikacji sieci Web** (nie zaznaczaj **aplikacji sieci Web (Model-View-Controller)**), a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="6ddb5-131">Upewnij się, że **platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**, a następnie wybierz przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-131">Make sure the **Target Framework** is set to **.NET Core 3.1**, and then select **Next**.</span></span>

* <span data-ttu-id="6ddb5-132">Nadaj nazwę \* SignalR rozmowie\*projektu, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="6ddb5-133">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="6ddb5-133">Add the SignalR client library</span></span>

<span data-ttu-id="6ddb5-134">SignalRBiblioteka serwerów jest dołączona do struktury udostępnionej ASP.NET Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="6ddb5-135">Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="6ddb5-136">W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="6ddb5-137">unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w npm, Menedżer pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="6ddb5-139">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="6ddb5-140">W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz pozycję **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="6ddb5-141">W obszarze **Biblioteka**wprowadź `@microsoft/signalr@latest` .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="6ddb5-142">Wybierz pozycję **Wybierz określone pliki**, rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *signalr.js* i *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="6ddb5-143">Ustaw **lokalizację docelową** na plik *wwwroot/js/signaler/*, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybór biblioteki](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="6ddb5-145">LibMan tworzy folder *wwwroot/js/sygnalizujący* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="6ddb5-147">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="6ddb5-148">Uruchom następujące polecenie, aby pobrać SignalR bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="6ddb5-149">Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="6ddb5-150">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="6ddb5-151">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="6ddb5-152">Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="6ddb5-153">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-153">Copy only the specified files.</span></span>

  <span data-ttu-id="6ddb5-154">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-155">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6ddb5-156">W **terminalu**Uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="6ddb5-157">Przejdź do folderu projektu (taki, który zawiera plik \* SignalR Chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="6ddb5-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="6ddb5-158">Uruchom następujące polecenie, aby pobrać SignalR bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="6ddb5-159">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="6ddb5-160">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="6ddb5-161">Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="6ddb5-162">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-162">Copy only the specified files.</span></span>

  <span data-ttu-id="6ddb5-163">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="6ddb5-164">Tworzenie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6ddb5-164">Create a SignalR hub</span></span>

<span data-ttu-id="6ddb5-165">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="6ddb5-166">W SignalR folderze projektu rozmowy utwórz folder *Hubs* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="6ddb5-167">W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="6ddb5-168">`ChatHub`Klasa dziedziczy z SignalR `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="6ddb5-169">`Hub`Klasa zarządza połączeniami, grupami i obsługą wiadomości.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="6ddb5-170">`SendMessage`Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="6ddb5-171">Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="6ddb5-172">SignalR kod jest asynchroniczny, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="6ddb5-173">Ponowne SignalR</span><span class="sxs-lookup"><span data-stu-id="6ddb5-173">Configure SignalR</span></span>

<span data-ttu-id="6ddb5-174">SignalRSerwer musi być skonfigurowany tak, aby przekazywać SignalR żądania do SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="6ddb5-175">Dodaj następujący wyróżniony kod do pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="6ddb5-176">Te zmiany są dodawane SignalR do ASP.NET Core systemów iniekcji i routingu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="6ddb5-177">Dodawanie SignalR kodu klienta</span><span class="sxs-lookup"><span data-stu-id="6ddb5-177">Add SignalR client code</span></span>

* <span data-ttu-id="6ddb5-178">Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="6ddb5-179">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-179">The preceding code:</span></span>

  * <span data-ttu-id="6ddb5-180">Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="6ddb5-181">Tworzy listę `id="messagesList"` na potrzeby wyświetlania komunikatów odbieranych z SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="6ddb5-182">Zawiera odwołania do skryptu do SignalR i kod aplikacji *chat.js* utworzony w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="6ddb5-183">W folderze *wwwroot/js* utwórz plik *chat.js* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="6ddb5-184">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-184">The preceding code:</span></span>

  * <span data-ttu-id="6ddb5-185">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="6ddb5-186">Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="6ddb5-187">Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6ddb5-188">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6ddb5-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6ddb5-190">Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6ddb5-192">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-193">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6ddb5-194">Z menu wybierz polecenie **uruchom > Uruchom bez debugowania**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="6ddb5-195">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="6ddb5-196">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="6ddb5-197">Nazwa i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-197">The name and message are displayed on both pages instantly.</span></span>

  ![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="6ddb5-199">Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="6ddb5-200">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="6ddb5-201">Załóżmy na przykład, że należy umieścić *signalr.js* w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="6ddb5-202">W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="6ddb5-203">![ Błąd nie znalezionosignalr.js](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="6ddb5-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="6ddb5-204">Jeśli zostanie wyświetlony komunikat o błędzie ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY w programie Chrome, uruchom następujące polecenia, aby zaktualizować certyfikat deweloperski:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ddb5-205">Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu programu SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="6ddb5-206">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="6ddb5-207">Utwórz projekt sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-207">Create a web project.</span></span>   
> * <span data-ttu-id="6ddb5-208">Dodaj SignalR bibliotekę kliencką.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="6ddb5-209">Utwórz SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="6ddb5-210">Skonfiguruj projekt do użycia SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="6ddb5-211">Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="6ddb5-212">Na końcu będziesz mieć działającą aplikację czatu:::: ![ no-Loc (signaler)::: przykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6ddb5-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="6ddb5-213">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6ddb5-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-216">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="6ddb5-217">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="6ddb5-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="6ddb5-219">Z menu wybierz pozycję **plik > nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="6ddb5-220">W oknie dialogowym **Nowy projekt** wybierz pozycję **zainstalowane > Visual C# > sieci Web > ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6ddb5-221">Nadaj nazwę \* SignalR rozmowie\*projektu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-221">Name the project *SignalRChat*.</span></span>   

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="6ddb5-223">Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa Razor stron.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="6ddb5-224">Wybierz platformę docelową programu **.NET Core**, wybierz pozycję **ASP.NET Core 2,2**, a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="6ddb5-227">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="6ddb5-228">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-229">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="6ddb5-230">Z menu wybierz pozycję **plik > nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="6ddb5-231">Wybierz pozycję **.NET Core > app > ASP.NET Core Web App** (nie wybieraj **ASP.NET Core Web App (MVC)**).</span><span class="sxs-lookup"><span data-stu-id="6ddb5-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="6ddb5-232">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-232">Select **Next**.</span></span>  

* <span data-ttu-id="6ddb5-233">Nadaj nazwę \* SignalR rozmowie\*projektu, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="6ddb5-234">Dodawanie SignalR biblioteki klienta</span><span class="sxs-lookup"><span data-stu-id="6ddb5-234">Add the SignalR client library</span></span> 

<span data-ttu-id="6ddb5-235">SignalRBiblioteka serwerów jest dołączona do `Microsoft.AspNetCore.App` pakietu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="6ddb5-236">Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="6ddb5-237">W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="6ddb5-238">unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w npm, Menedżer pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="6ddb5-240">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="6ddb5-241">W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz pozycję **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="6ddb5-242">W polu **Biblioteka**wprowadź `@microsoft/signalr@3` i wybierz najnowszą wersję, która nie jest w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybór biblioteki](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="6ddb5-244">Wybierz pozycję **Wybierz określone pliki**, rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *signalr.js* i *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="6ddb5-245">Ustaw **lokalizację docelową** na plik *wwwroot/lib/sygnalizujący/*, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — Wybieranie plików i lokalizacji docelowej](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="6ddb5-247">LibMan tworzy folder *wwwroot/lib/sygnalizujący* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="6ddb5-249">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="6ddb5-250">Uruchom następujące polecenie, aby pobrać SignalR bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="6ddb5-251">Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="6ddb5-252">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="6ddb5-253">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="6ddb5-254">Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="6ddb5-255">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="6ddb5-256">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-257">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="6ddb5-258">W **terminalu**Uruchom następujące polecenie, aby zainstalować LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="6ddb5-259">Przejdź do folderu projektu (taki, który zawiera plik \* SignalR Chat. csproj\* ).</span><span class="sxs-lookup"><span data-stu-id="6ddb5-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="6ddb5-260">Uruchom następujące polecenie, aby pobrać SignalR bibliotekę kliencką przy użyciu LibMan.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="6ddb5-261">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="6ddb5-262">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="6ddb5-263">Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="6ddb5-264">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="6ddb5-265">Dane wyjściowe wyglądają podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="6ddb5-266">Tworzenie SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="6ddb5-266">Create a SignalR hub</span></span>   

<span data-ttu-id="6ddb5-267">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="6ddb5-268">W SignalR folderze projektu rozmowy utwórz folder *Hubs* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="6ddb5-269">W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="6ddb5-270">`ChatHub`Klasa dziedziczy z SignalR `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="6ddb5-271">`Hub`Klasa zarządza połączeniami, grupami i obsługą wiadomości.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="6ddb5-272">`SendMessage`Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="6ddb5-273">Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="6ddb5-274">SignalR kod jest asynchroniczny, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="6ddb5-275">Ponowne SignalR</span><span class="sxs-lookup"><span data-stu-id="6ddb5-275">Configure SignalR</span></span>  

<span data-ttu-id="6ddb5-276">SignalRSerwer musi być skonfigurowany tak, aby przekazywać SignalR żądania do SignalR .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="6ddb5-277">Dodaj następujący wyróżniony kod do pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="6ddb5-278">Te zmiany są dodawane SignalR do ASP.NET Core systemu iniekcji zależności oraz potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="6ddb5-279">Dodawanie SignalR kodu klienta</span><span class="sxs-lookup"><span data-stu-id="6ddb5-279">Add SignalR client code</span></span>    

* <span data-ttu-id="6ddb5-280">Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="6ddb5-281">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-281">The preceding code:</span></span>   

  * <span data-ttu-id="6ddb5-282">Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="6ddb5-283">Tworzy listę `id="messagesList"` na potrzeby wyświetlania komunikatów odbieranych z SignalR centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="6ddb5-284">Zawiera odwołania do skryptu do SignalR i kod aplikacji *chat.js* utworzony w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="6ddb5-285">W folderze *wwwroot/js* utwórz plik *chat.js* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="6ddb5-286">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-286">The preceding code:</span></span>   

  * <span data-ttu-id="6ddb5-287">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="6ddb5-288">Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="6ddb5-289">Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="6ddb5-290">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6ddb5-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="6ddb5-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6ddb5-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="6ddb5-292">Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="6ddb5-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6ddb5-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6ddb5-294">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="6ddb5-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6ddb5-295">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="6ddb5-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6ddb5-296">Z menu wybierz polecenie **uruchom > Uruchom bez debugowania**.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="6ddb5-297">Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="6ddb5-298">Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .</span><span class="sxs-lookup"><span data-stu-id="6ddb5-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="6ddb5-299">Nazwa i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-299">The name and message are displayed on both pages instantly.</span></span>   

  ![::: No-Loc (Sygnalizującer)::: przykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="6ddb5-301">Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="6ddb5-302">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="6ddb5-303">Załóżmy na przykład, że należy umieścić *signalr.js* w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="6ddb5-304">W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="6ddb5-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="6ddb5-305">![ Błąd nie znalezionosignalr.js](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="6ddb5-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="6ddb5-306">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6ddb5-306">Additional resources</span></span> 
* [<span data-ttu-id="6ddb5-307">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="6ddb5-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
