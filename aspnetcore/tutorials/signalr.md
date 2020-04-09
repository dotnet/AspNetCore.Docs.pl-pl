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
ms.openlocfilehash: 55ebdbfa4556deca74a6cdf0638307425cd1a01a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666748"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Samouczek: Wprowadzenie do ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu sieci Web.
> * Dodaj bibliotekę klienta SignalR.
> * Utwórz koncentrator SignalR.
> * Skonfiguruj projekt do używania SignalR.
> * Dodaj kod, który wysyła wiadomości z dowolnego klienta do wszystkich połączonych klientów.

Na koniec będziesz mieć działającą aplikację czatu:

![Przykładowa aplikacja SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

* Z menu wybierz **polecenie Plik > nowy projekt**.

* W oknie **dialogowym Tworzenie nowego projektu** wybierz **pozycję ASP.NET podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.

* W oknie dialogowym **Konfigurowanie nowego projektu** nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.

* W oknie **dialogowym Tworzenie nowej aplikacji sieci Web ASP.NET Core** wybierz pozycję **.NET Core** i ASP.NET Core **3.0**. 

* Wybierz **aplikację sieci Web,** aby utworzyć projekt, który używa stron Razor, a następnie wybierz pozycję **Utwórz**.

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.

* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz **polecenie Plik > Nowe rozwiązanie**.

* Wybierz opcję **.NET Core > App > Web Application** (Nie wybieraj aplikacji sieci Web **(Administrator widoku modelu)**), a następnie wybierz pozycję **Dalej**.

* Upewnij się, że **struktura docelowa** jest ustawiona na **.NET Core 3.0**, a następnie wybierz przycisk **Dalej**.

* Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.

---

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta SignalR

Biblioteka serwera SignalR znajduje się w udostępnionej ramach ASP.NET Core 3.0. Biblioteka klienta JavaScript nie jest automatycznie uwzględniana w projekcie. W tym samouczku użyj Menedżera biblioteki (LibMan), aby uzyskać bibliotekę klienta z *unpkg*. unpkg to sieć dostarczania zawartości (CDN)), która może dostarczyć wszystko, co można znaleźć w npm, menedżerze pakietów Node.js.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.

* W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz **opcję unpkg**.

* W przypadku `@microsoft/signalr@latest` **biblioteki**wprowadź .

* Wybierz **wybierz wybierz opcję Wybierz określone pliki,** rozwiń folder *dist/przeglądarki* i wybierz *signalr.js* i *signalr.min.js*.

* Ustaw **lokalizację docelową** na *wwwroot/js/signalr/* i wybierz **pozycję Zainstaluj**.

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybierz bibliotekę](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan tworzy folder *wwwroot/js/signalr* i kopiuje do niego wybrane pliki.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować libman.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman. Może być trzeba odczekać kilka sekund przed widząc dane wyjściowe.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry określają następujące opcje:
  * Użyj dostawcy unpkg.
  * Skopiuj pliki do miejsca docelowego *wwwroot/js/signalr.*
  * Skopiuj tylko określone pliki.

  Dane wyjściowe wyglądają następująco:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W **terminalu**uruchom następujące polecenie, aby zainstalować libman.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Przejdź do folderu projektu (tego, który zawiera plik *SignalRChat.csproj).*

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry określają następujące opcje:
  * Użyj dostawcy unpkg.
  * Skopiuj pliki do miejsca docelowego *wwwroot/js/signalr.*
  * Skopiuj tylko określone pliki.

  Dane wyjściowe wyglądają następująco:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Tworzenie koncentratora signalr

*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.

* W folderze projektu SignalRChat utwórz folder *Koncentratorów.*

* W folderze *Koncentratory* utwórz plik *ChatHub.cs* z następującym kodem:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Klasa `ChatHub` dziedziczy z SignalR `Hub` klasy. Klasa `Hub` zarządza połączeniami, grupami i wiadomościami.

  Metoda `SendMessage` może być wywoływana przez połączonego klienta, aby wysłać wiadomość do wszystkich klientów. Kod klienta JavaScript, który wywołuje metodę jest wyświetlany w dalszej części samouczka. Kod signalr jest asynchroniczne, aby zapewnić maksymalną skalowalność.

## <a name="configure-signalr"></a>Konfigurowanie sygnału

Serwer SignalR musi być skonfigurowany do przekazywania żądań SignalR do SignalR.

* Dodaj poniższy wyróżniony kod do pliku *Startup.cs.*

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Te zmiany dodać SignalR do ASP.NET Core systemów iniekcji zależności i routingu.

## <a name="add-signalr-client-code"></a>Dodaj kod klienta SignalR

* Zastąp zawartość w *pages\Index.cshtml* następującym kodem:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Powyższy kod ma następujące działanie:

  * Tworzy pola tekstowe dla nazwy i tekstu wiadomości oraz przycisku Prześlij.
  * Tworzy listę `id="messagesList"` do wyświetlania komunikatów, które są odbierane z centrum SignalR.
  * Zawiera odwołania do skryptów signalr i *chat.js* kod aplikacji, które można utworzyć w następnym kroku.

* W folderze *wwwroot/js* utwórz plik *chat.js* z następującym kodem:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Powyższy kod ma następujące działanie:

  * Tworzy i uruchamia połączenie.
  * Dodaje do przycisku prześlij program obsługi, który wysyła wiadomości do centrum.
  * Dodaje do obiektu połączenia program obsługi, który odbiera wiadomości z koncentratora i dodaje je do listy.

## <a name="run-the-app"></a>Uruchomienie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Naciśnij **klawisze CTRL+F5,** aby uruchomić aplikację bez debugowania.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W zintegrowanym terminalu uruchom następujące polecenie:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz **polecenie Uruchom > Start bez debugowania**.

---

* Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

* Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij wiadomość.**

  Nazwa i wiadomość są natychmiast wyświetlane na obu stronach.

  ![Przykładowa aplikacja SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Jeśli aplikacja nie działa, otwórz narzędzia programistyczne przeglądarki (F12) i przejdź do konsoli. Mogą pojawić się błędy związane z kodem HTML i JavaScript. Załóżmy na przykład, że plik *signalr.js* zostanie umieszczony w innym folderze niż skierowany. W takim przypadku odwołanie do tego pliku nie będzie działać i zobaczysz błąd 404 w konsoli.
>   ![nie znaleziono błędu signalr.js](signalr/_static/3.x/f12-console.png)
> * Jeśli w Chrome zostanie wyświetlony błąd ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY, uruchom następujące polecenia, aby zaktualizować certyfikat dewelopera:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR. Omawiane kwestie:   

> [!div class="checklist"]  
> * Tworzenie projektu sieci Web.   
> * Dodaj bibliotekę klienta SignalR. 
> * Utwórz koncentrator SignalR.   
> * Skonfiguruj projekt do używania SignalR.   
> * Dodaj kod, który wysyła wiadomości z dowolnego klienta do wszystkich połączonych klientów.  
Na końcu będziesz mieć działającą aplikację ![czatu: przykładową aplikację SignalR](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Wymagania wstępne    

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web 

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)  

* Z menu wybierz **polecenie Plik > nowy projekt**. 

* W oknie dialogowym **Nowy projekt** wybierz pozycję **Zainstalowana > > sieci Web > > ASP.NET podstawowej aplikacji sieci Web**w języku Web > Visual C#. Nazwij projekt *SignalRChat*. 

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Wybierz **aplikację sieci Web,** aby utworzyć projekt, który używa stron Razor. 

* Wybierz docelową strukturę **.NET Core**, wybierz **ASP.NET Core 2.2**i kliknij **przycisk OK**.    

  ![Okno dialogowe Nowy projekt w programie Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.  

* Uruchom następujące polecenia:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

* Z menu wybierz **polecenie Plik > Nowe rozwiązanie**.    

* Wybierz opcję **.NET Core > App > ASP.NET Core Web App** (Nie **wybieraj aplikacji ASP.NET Core Web App (MVC).**  

* Wybierz **pozycję Dalej**.  

* Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.   

--- 

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta SignalR   

Biblioteka serwera SignalR znajduje `Microsoft.AspNetCore.App` się w metapakiecie. Biblioteka klienta JavaScript nie jest automatycznie uwzględniana w projekcie. W tym samouczku użyj Menedżera biblioteki (LibMan), aby uzyskać bibliotekę klienta z *unpkg*. unpkg to sieć dostarczania zawartości (CDN)), która może dostarczyć wszystko, co można znaleźć w npm, menedżerze pakietów Node.js.    

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)  

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.  

* W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz **opcję unpkg**. 

* W obszarze `@microsoft/signalr@3` **Biblioteka**wprowadź i wybierz najnowszą wersję, która nie jest w wersji zapoznawczej.  

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybierz bibliotekę](signalr/_static/2.x/libman1.png)   

* Wybierz **wybierz wybierz opcję Wybierz określone pliki,** rozwiń folder *dist/przeglądarki* i wybierz *signalr.js* i *signalr.min.js*. 

* Ustaw **lokalizację docelową** na *wwwroot/lib/signalr/* i wybierz **pozycję Zainstaluj**.    

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybieranie plików i miejsca docelowego](signalr/_static/2.x/libman2.png) 

  LibMan tworzy folder *wwwroot/lib/signalr* i kopiuje do niego wybrane pliki.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować libman.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman. Może być trzeba odczekać kilka sekund przed widząc dane wyjściowe.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry określają następujące opcje: 
  * Użyj dostawcy unpkg. 
  * Skopiuj pliki do miejsca docelowego *wwwroot/lib/signalr.*    
  * Skopiuj tylko określone pliki.  

  Dane wyjściowe wyglądają następująco:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

* W **terminalu**uruchom następujące polecenie, aby zainstalować libman. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Przejdź do folderu projektu (tego, który zawiera plik *SignalRChat.csproj).* 

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta SignalR przy użyciu libman.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry określają następujące opcje: 
  * Użyj dostawcy unpkg. 
  * Skopiuj pliki do miejsca docelowego *wwwroot/lib/signalr.*    
  * Skopiuj tylko określone pliki.  

  Dane wyjściowe wyglądają następująco:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Tworzenie koncentratora signalr 

*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.   

* W folderze projektu SignalRChat utwórz folder *Koncentratorów.*    

* W folderze *Koncentratory* utwórz plik *ChatHub.cs* z następującym kodem: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Klasa `ChatHub` dziedziczy z SignalR `Hub` klasy. Klasa `Hub` zarządza połączeniami, grupami i wiadomościami.    

  Metoda `SendMessage` może być wywoływana przez połączonego klienta, aby wysłać wiadomość do wszystkich klientów. Kod klienta JavaScript, który wywołuje metodę jest wyświetlany w dalszej części samouczka. Kod signalr jest asynchroniczne, aby zapewnić maksymalną skalowalność.  

## <a name="configure-signalr"></a>Konfigurowanie sygnału    

Serwer SignalR musi być skonfigurowany do przekazywania żądań SignalR do SignalR.  

* Dodaj poniższy wyróżniony kod do pliku *Startup.cs.*  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Te zmiany dodać SignalR do ASP.NET core systemu iniekcji zależności i potoku oprogramowania pośredniczącego.    

## <a name="add-signalr-client-code"></a>Dodaj kod klienta SignalR  

* Zastąp zawartość w *pages\Index.cshtml* następującym kodem:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Powyższy kod ma następujące działanie:   

  * Tworzy pola tekstowe dla nazwy i tekstu wiadomości oraz przycisku Prześlij.  
  * Tworzy listę `id="messagesList"` do wyświetlania komunikatów, które są odbierane z centrum SignalR. 
  * Zawiera odwołania do skryptów signalr i *chat.js* kod aplikacji, które można utworzyć w następnym kroku.  

* W folderze *wwwroot/js* utwórz plik *chat.js* z następującym kodem:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Powyższy kod ma następujące działanie:   

  * Tworzy i uruchamia połączenie.    
  * Dodaje do przycisku prześlij program obsługi, który wysyła wiadomości do centrum. 
  * Dodaje do obiektu połączenia program obsługi, który odbiera wiadomości z koncentratora i dodaje je do listy.  

## <a name="run-the-app"></a>Uruchomienie aplikacji  

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)   

* Naciśnij **klawisze CTRL+F5,** aby uruchomić aplikację bez debugowania.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* W zintegrowanym terminalu uruchom następujące polecenie:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz **polecenie Uruchom > Start bez debugowania**.

---

* Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

* Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij wiadomość.**  

  Nazwa i wiadomość są natychmiast wyświetlane na obu stronach.   

  ![SignalRprzykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Jeśli aplikacja nie działa, otwórz narzędzia programistyczne przeglądarki (F12) i przejdź do konsoli. Mogą pojawić się błędy związane z kodem HTML i JavaScript. Załóżmy na przykład, że plik *signalr.js* zostanie umieszczony w innym folderze niż skierowany. W takim przypadku odwołanie do tego pliku nie będzie działać i zobaczysz błąd 404 w konsoli.   
> ![nie znaleziono błędu signalr.js](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Zasoby dodatkowe 
* [Wersja youtube tego samouczka](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
