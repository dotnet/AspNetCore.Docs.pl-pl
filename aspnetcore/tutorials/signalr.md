---
title: Wprowadzenie do ASP.NET CoreSignalR
author: bradygaster
description: W tym samouczku utworzysz aplikację do czatu, która używa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994620"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="33d8b-103">Samouczek: Wprowadzenie do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="33d8b-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33d8b-104">Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="33d8b-105">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="33d8b-106">Tworzenie projektu sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33d8b-106">Create a web project.</span></span>
> * <span data-ttu-id="33d8b-107">Dodaj bibliotekę klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="33d8b-108">Utwórz koncentrator SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="33d8b-109">Skonfiguruj projekt do używania SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="33d8b-110">Dodaj kod, który wysyła wiadomości z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="33d8b-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="33d8b-111">Na koniec będziesz mieć działającą aplikację czatu:</span><span class="sxs-lookup"><span data-stu-id="33d8b-111">At the end, you'll have a working chat app:</span></span>

![Przykładowa aplikacja SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="33d8b-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="33d8b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="33d8b-117">Tworzenie projektu aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="33d8b-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="33d8b-119">Z menu wybierz **polecenie Plik > nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="33d8b-120">W oknie **dialogowym Tworzenie nowego projektu** wybierz **pozycję ASP.NET podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="33d8b-121">W oknie dialogowym **Konfigurowanie nowego projektu** nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="33d8b-122">W oknie **dialogowym Tworzenie nowej aplikacji sieci Web ASP.NET Core** wybierz pozycję **.NET Core** i ASP.NET Core **3.0**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="33d8b-123">Wybierz **aplikację sieci Web,** aby utworzyć projekt, który używa stron Razor, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="33d8b-126">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="33d8b-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="33d8b-127">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="33d8b-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-128">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33d8b-129">Z menu wybierz **polecenie Plik > Nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="33d8b-130">Wybierz opcję **.NET Core > App > Web Application** (Nie wybieraj aplikacji sieci Web **(Administrator widoku modelu)**), a następnie wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="33d8b-131">Upewnij się, że **struktura docelowa** jest ustawiona na **.NET Core 3.0**, a następnie wybierz przycisk **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="33d8b-132">Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="33d8b-133">Dodawanie biblioteki klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="33d8b-133">Add the SignalR client library</span></span>

<span data-ttu-id="33d8b-134">Biblioteka serwera SignalR znajduje się w udostępnionej ramach ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="33d8b-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="33d8b-135">Biblioteka klienta JavaScript nie jest automatycznie uwzględniana w projekcie.</span><span class="sxs-lookup"><span data-stu-id="33d8b-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="33d8b-136">W tym samouczku użyj Menedżera biblioteki (LibMan), aby uzyskać bibliotekę klienta z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="33d8b-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="33d8b-137">unpkg to sieć dostarczania zawartości (CDN), która może dostarczyć wszystko, co można znaleźć w npm, menedżerze pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="33d8b-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-138">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="33d8b-139">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="33d8b-140">W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz **opcję unpkg**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="33d8b-141">W przypadku `@microsoft/signalr@latest` **biblioteki**wprowadź .</span><span class="sxs-lookup"><span data-stu-id="33d8b-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="33d8b-142">Wybierz **wybierz wybierz opcję Wybierz określone pliki,** rozwiń folder *dist/przeglądarki* i wybierz *signalr.js* i *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="33d8b-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="33d8b-143">Ustaw **lokalizację docelową** na *wwwroot/js/signalr/* i wybierz **pozycję Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybierz bibliotekę](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="33d8b-145">LibMan tworzy folder *wwwroot/js/signalr* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="33d8b-147">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="33d8b-148">Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="33d8b-149">Może być trzeba odczekać kilka sekund przed widząc dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="33d8b-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="33d8b-150">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="33d8b-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="33d8b-151">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="33d8b-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="33d8b-152">Skopiuj pliki do miejsca docelowego *wwwroot/js/signalr.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="33d8b-153">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-153">Copy only the specified files.</span></span>

  <span data-ttu-id="33d8b-154">Dane wyjściowe wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="33d8b-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-155">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33d8b-156">W **terminalu**uruchom następujące polecenie, aby zainstalować libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="33d8b-157">Przejdź do folderu projektu (tego, który zawiera plik *SignalRChat.csproj).*</span><span class="sxs-lookup"><span data-stu-id="33d8b-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="33d8b-158">Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="33d8b-159">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="33d8b-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="33d8b-160">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="33d8b-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="33d8b-161">Skopiuj pliki do miejsca docelowego *wwwroot/js/signalr.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="33d8b-162">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-162">Copy only the specified files.</span></span>

  <span data-ttu-id="33d8b-163">Dane wyjściowe wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="33d8b-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="33d8b-164">Tworzenie koncentratora signalr</span><span class="sxs-lookup"><span data-stu-id="33d8b-164">Create a SignalR hub</span></span>

<span data-ttu-id="33d8b-165">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="33d8b-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="33d8b-166">W folderze projektu SignalRChat utwórz folder *Koncentratorów.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="33d8b-167">W folderze *Koncentratory* utwórz plik *ChatHub.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="33d8b-168">Klasa `ChatHub` dziedziczy z SignalR `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="33d8b-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="33d8b-169">Klasa `Hub` zarządza połączeniami, grupami i wiadomościami.</span><span class="sxs-lookup"><span data-stu-id="33d8b-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="33d8b-170">Metoda `SendMessage` może być wywoływana przez połączonego klienta, aby wysłać wiadomość do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="33d8b-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="33d8b-171">Kod klienta JavaScript, który wywołuje metodę jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="33d8b-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="33d8b-172">Kod signalr jest asynchroniczne, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="33d8b-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="33d8b-173">Konfigurowanie sygnału</span><span class="sxs-lookup"><span data-stu-id="33d8b-173">Configure SignalR</span></span>

<span data-ttu-id="33d8b-174">Serwer SignalR musi być skonfigurowany do przekazywania żądań SignalR do SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="33d8b-175">Dodaj poniższy wyróżniony kod do pliku *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="33d8b-176">Te zmiany dodać SignalR do ASP.NET Core systemów iniekcji zależności i routingu.</span><span class="sxs-lookup"><span data-stu-id="33d8b-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="33d8b-177">Dodaj kod klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="33d8b-177">Add SignalR client code</span></span>

* <span data-ttu-id="33d8b-178">Zastąp zawartość w *pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="33d8b-179">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-179">The preceding code:</span></span>

  * <span data-ttu-id="33d8b-180">Tworzy pola tekstowe dla nazwy i tekstu wiadomości oraz przycisku Prześlij.</span><span class="sxs-lookup"><span data-stu-id="33d8b-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="33d8b-181">Tworzy listę `id="messagesList"` do wyświetlania komunikatów, które są odbierane z centrum SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="33d8b-182">Zawiera odwołania do skryptów signalr i *chat.js* kod aplikacji, które można utworzyć w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="33d8b-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="33d8b-183">W folderze *wwwroot/js* utwórz plik *chat.js* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="33d8b-184">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-184">The preceding code:</span></span>

  * <span data-ttu-id="33d8b-185">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="33d8b-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="33d8b-186">Dodaje do przycisku prześlij program obsługi, który wysyła wiadomości do centrum.</span><span class="sxs-lookup"><span data-stu-id="33d8b-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="33d8b-187">Dodaje do obiektu połączenia program obsługi, który odbiera wiadomości z koncentratora i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="33d8b-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="33d8b-188">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="33d8b-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-189">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="33d8b-190">Naciśnij **klawisze CTRL+F5,** aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="33d8b-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="33d8b-192">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-193">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33d8b-194">Z menu wybierz **polecenie Uruchom > Start bez debugowania**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="33d8b-195">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="33d8b-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="33d8b-196">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij wiadomość.**</span><span class="sxs-lookup"><span data-stu-id="33d8b-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="33d8b-197">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="33d8b-197">The name and message are displayed on both pages instantly.</span></span>

  ![Przykładowa aplikacja SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="33d8b-199">Jeśli aplikacja nie działa, otwórz narzędzia programistyczne przeglądarki (F12) i przejdź do konsoli.</span><span class="sxs-lookup"><span data-stu-id="33d8b-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="33d8b-200">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33d8b-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="33d8b-201">Załóżmy na przykład, że plik *signalr.js* zostanie umieszczony w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="33d8b-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="33d8b-202">W takim przypadku odwołanie do tego pliku nie będzie działać i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="33d8b-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="33d8b-203">![nie znaleziono błędu signalr.js](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="33d8b-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="33d8b-204">Jeśli w Chrome zostanie wyświetlony błąd ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY, uruchom następujące polecenia, aby zaktualizować certyfikat dewelopera:</span><span class="sxs-lookup"><span data-stu-id="33d8b-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="33d8b-205">Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="33d8b-206">Omawiane kwestie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-206">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="33d8b-207">Tworzenie projektu sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33d8b-207">Create a web project.</span></span>   
> * <span data-ttu-id="33d8b-208">Dodaj bibliotekę klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-208">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="33d8b-209">Utwórz koncentrator SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-209">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="33d8b-210">Skonfiguruj projekt do używania SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-210">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="33d8b-211">Dodaj kod, który wysyła wiadomości z dowolnego klienta do wszystkich połączonych klientów.</span><span class="sxs-lookup"><span data-stu-id="33d8b-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="33d8b-212">Na końcu będziesz mieć działającą aplikację ![czatu: przykładową aplikację SignalR](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="33d8b-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="33d8b-213">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="33d8b-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-214">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-216">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="33d8b-217">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="33d8b-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-218">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="33d8b-219">Z menu wybierz **polecenie Plik > nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="33d8b-220">W oknie dialogowym **Nowy projekt** wybierz pozycję **Zainstalowana > > sieci Web > > ASP.NET podstawowej aplikacji sieci Web**w języku Web > Visual C#.</span><span class="sxs-lookup"><span data-stu-id="33d8b-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="33d8b-221">Nazwij projekt *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="33d8b-221">Name the project *SignalRChat*.</span></span> 

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="33d8b-223">Wybierz **aplikację sieci Web,** aby utworzyć projekt, który używa stron Razor.</span><span class="sxs-lookup"><span data-stu-id="33d8b-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="33d8b-224">Wybierz docelową strukturę **.NET Core**, wybierz **ASP.NET Core 2.2**i kliknij **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="33d8b-227">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.</span><span class="sxs-lookup"><span data-stu-id="33d8b-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="33d8b-228">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="33d8b-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-229">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="33d8b-230">Z menu wybierz **polecenie Plik > Nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="33d8b-231">Wybierz opcję **.NET Core > App > ASP.NET Core Web App** (Nie **wybieraj aplikacji ASP.NET Core Web App (MVC).**</span><span class="sxs-lookup"><span data-stu-id="33d8b-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="33d8b-232">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-232">Select **Next**.</span></span>  

* <span data-ttu-id="33d8b-233">Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="33d8b-234">Dodawanie biblioteki klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="33d8b-234">Add the SignalR client library</span></span>   

<span data-ttu-id="33d8b-235">Biblioteka serwera SignalR znajduje `Microsoft.AspNetCore.App` się w metapakiecie.</span><span class="sxs-lookup"><span data-stu-id="33d8b-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="33d8b-236">Biblioteka klienta JavaScript nie jest automatycznie uwzględniana w projekcie.</span><span class="sxs-lookup"><span data-stu-id="33d8b-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="33d8b-237">W tym samouczku użyj Menedżera biblioteki (LibMan), aby uzyskać bibliotekę klienta z *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="33d8b-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="33d8b-238">unpkg to sieć dostarczania zawartości (CDN), która może dostarczyć wszystko, co można znaleźć w npm, menedżerze pakietów Node.js.</span><span class="sxs-lookup"><span data-stu-id="33d8b-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-239">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="33d8b-240">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="33d8b-241">W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz **opcję unpkg**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="33d8b-242">W obszarze `@microsoft/signalr@3` **Biblioteka**wprowadź i wybierz najnowszą wersję, która nie jest w wersji zapoznawczej.</span><span class="sxs-lookup"><span data-stu-id="33d8b-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybierz bibliotekę](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="33d8b-244">Wybierz **wybierz wybierz opcję Wybierz określone pliki,** rozwiń folder *dist/przeglądarki* i wybierz *signalr.js* i *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="33d8b-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="33d8b-245">Ustaw **lokalizację docelową** na *wwwroot/lib/signalr/* i wybierz **pozycję Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybieranie plików i miejsca docelowego](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="33d8b-247">LibMan tworzy folder *wwwroot/lib/signalr* i kopiuje do niego wybrane pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="33d8b-249">W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="33d8b-250">Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="33d8b-251">Może być trzeba odczekać kilka sekund przed widząc dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="33d8b-251">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="33d8b-252">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="33d8b-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="33d8b-253">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="33d8b-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="33d8b-254">Skopiuj pliki do miejsca docelowego *wwwroot/lib/signalr.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="33d8b-255">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="33d8b-256">Dane wyjściowe wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="33d8b-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-257">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="33d8b-258">W **terminalu**uruchom następujące polecenie, aby zainstalować libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="33d8b-259">Przejdź do folderu projektu (tego, który zawiera plik *SignalRChat.csproj).*</span><span class="sxs-lookup"><span data-stu-id="33d8b-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="33d8b-260">Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.</span><span class="sxs-lookup"><span data-stu-id="33d8b-260">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="33d8b-261">Parametry określają następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="33d8b-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="33d8b-262">Użyj dostawcy unpkg.</span><span class="sxs-lookup"><span data-stu-id="33d8b-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="33d8b-263">Skopiuj pliki do miejsca docelowego *wwwroot/lib/signalr.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="33d8b-264">Skopiuj tylko określone pliki.</span><span class="sxs-lookup"><span data-stu-id="33d8b-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="33d8b-265">Dane wyjściowe wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="33d8b-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="33d8b-266">Tworzenie koncentratora signalr</span><span class="sxs-lookup"><span data-stu-id="33d8b-266">Create a SignalR hub</span></span> 

<span data-ttu-id="33d8b-267">*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="33d8b-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="33d8b-268">W folderze projektu SignalRChat utwórz folder *Koncentratorów.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="33d8b-269">W folderze *Koncentratory* utwórz plik *ChatHub.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="33d8b-270">Klasa `ChatHub` dziedziczy z SignalR `Hub` klasy.</span><span class="sxs-lookup"><span data-stu-id="33d8b-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="33d8b-271">Klasa `Hub` zarządza połączeniami, grupami i wiadomościami.</span><span class="sxs-lookup"><span data-stu-id="33d8b-271">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="33d8b-272">Metoda `SendMessage` może być wywoływana przez połączonego klienta, aby wysłać wiadomość do wszystkich klientów.</span><span class="sxs-lookup"><span data-stu-id="33d8b-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="33d8b-273">Kod klienta JavaScript, który wywołuje metodę jest wyświetlany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="33d8b-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="33d8b-274">Kod signalr jest asynchroniczne, aby zapewnić maksymalną skalowalność.</span><span class="sxs-lookup"><span data-stu-id="33d8b-274">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="33d8b-275">Konfigurowanie sygnału</span><span class="sxs-lookup"><span data-stu-id="33d8b-275">Configure SignalR</span></span>    

<span data-ttu-id="33d8b-276">Serwer SignalR musi być skonfigurowany do przekazywania żądań SignalR do SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="33d8b-277">Dodaj poniższy wyróżniony kod do pliku *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="33d8b-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="33d8b-278">Te zmiany dodać SignalR do ASP.NET core systemu iniekcji zależności i potoku oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="33d8b-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="33d8b-279">Dodaj kod klienta SignalR</span><span class="sxs-lookup"><span data-stu-id="33d8b-279">Add SignalR client code</span></span>  

* <span data-ttu-id="33d8b-280">Zastąp zawartość w *pages\Index.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="33d8b-281">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-281">The preceding code:</span></span>   

  * <span data-ttu-id="33d8b-282">Tworzy pola tekstowe dla nazwy i tekstu wiadomości oraz przycisku Prześlij.</span><span class="sxs-lookup"><span data-stu-id="33d8b-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="33d8b-283">Tworzy listę `id="messagesList"` do wyświetlania komunikatów, które są odbierane z centrum SignalR.</span><span class="sxs-lookup"><span data-stu-id="33d8b-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="33d8b-284">Zawiera odwołania do skryptów signalr i *chat.js* kod aplikacji, które można utworzyć w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="33d8b-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="33d8b-285">W folderze *wwwroot/js* utwórz plik *chat.js* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33d8b-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="33d8b-286">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-286">The preceding code:</span></span>   

  * <span data-ttu-id="33d8b-287">Tworzy i uruchamia połączenie.</span><span class="sxs-lookup"><span data-stu-id="33d8b-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="33d8b-288">Dodaje do przycisku prześlij program obsługi, który wysyła wiadomości do centrum.</span><span class="sxs-lookup"><span data-stu-id="33d8b-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="33d8b-289">Dodaje do obiektu połączenia program obsługi, który odbiera wiadomości z koncentratora i dodaje je do listy.</span><span class="sxs-lookup"><span data-stu-id="33d8b-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="33d8b-290">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="33d8b-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="33d8b-291">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33d8b-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="33d8b-292">Naciśnij **klawisze CTRL+F5,** aby uruchomić aplikację bez debugowania.</span><span class="sxs-lookup"><span data-stu-id="33d8b-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="33d8b-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33d8b-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="33d8b-294">W zintegrowanym terminalu uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="33d8b-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="33d8b-295">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="33d8b-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33d8b-296">Z menu wybierz **polecenie Uruchom > Start bez debugowania**.</span><span class="sxs-lookup"><span data-stu-id="33d8b-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="33d8b-297">Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="33d8b-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="33d8b-298">Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij wiadomość.**</span><span class="sxs-lookup"><span data-stu-id="33d8b-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="33d8b-299">Nazwa i wiadomość są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="33d8b-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="33d8b-300">![SignalRprzykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="33d8b-300">![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="33d8b-301">Jeśli aplikacja nie działa, otwórz narzędzia programistyczne przeglądarki (F12) i przejdź do konsoli.</span><span class="sxs-lookup"><span data-stu-id="33d8b-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="33d8b-302">Mogą pojawić się błędy związane z kodem HTML i JavaScript.</span><span class="sxs-lookup"><span data-stu-id="33d8b-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="33d8b-303">Załóżmy na przykład, że plik *signalr.js* zostanie umieszczony w innym folderze niż skierowany.</span><span class="sxs-lookup"><span data-stu-id="33d8b-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="33d8b-304">W takim przypadku odwołanie do tego pliku nie będzie działać i zobaczysz błąd 404 w konsoli.</span><span class="sxs-lookup"><span data-stu-id="33d8b-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="33d8b-305">![nie znaleziono błędu signalr.js](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="33d8b-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="33d8b-306">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="33d8b-306">Additional resources</span></span> 
* [<span data-ttu-id="33d8b-307">Wersja youtube tego samouczka</span><span class="sxs-lookup"><span data-stu-id="33d8b-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
