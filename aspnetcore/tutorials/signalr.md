---
title: Wprowadzenie do ASP.NET CoreSignalR
author: bradygaster
description: W tym samouczku utworzysz aplikację czatu korzystającą ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 3fab97781fe354fd3d244880a00353957d7cfabf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774564"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Samouczek: Rozpoczynanie pracy z usługą ASP.NET Core sygnalizujący

::: moniker range=">= aspnetcore-3.0"

W tym samouczku przedstawiono podstawy tworzenia aplikacji w czasie rzeczywistym przy użyciu usługi sygnalizującej. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz projekt sieci Web.
> * Dodaj bibliotekę klienta sygnalizującego.
> * Utwórz centrum sygnałów.
> * Skonfiguruj projekt do używania sygnalizującego.
> * Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.

Na końcu będziesz mieć działającą aplikację czatu:

![Przykładowa aplikacja sygnalizująca](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Z menu wybierz pozycję **plik > nowy projekt**.

* W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web**, a następnie wybierz przycisk **dalej**.

* W oknie dialogowym **Konfigurowanie nowego projektu** Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.

* W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **.net Core** i **ASP.NET Core 3,0**. 

* Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa Razor Pages, a następnie wybierz pozycję **Utwórz**.

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.

* Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz pozycję **plik > nowe rozwiązanie**.

* Wybierz pozycję **.NET Core > App > aplikacji sieci Web** (nie zaznaczaj **aplikacji sieci Web (Model-View-Controller)**), a następnie wybierz przycisk **dalej**.

* Upewnij się, że **platforma docelowa** jest ustawiona na **platformę .NET Core 3,0**, a następnie wybierz przycisk **dalej**.

* Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.

---

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta sygnalizującego

Biblioteka serwera sygnalizującego jest dołączona do struktury udostępnionej ASP.NET Core 3,0. Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu. W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg*. unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w programie npm, w Menedżerze pakietów środowiska Node. js.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.

* W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz pozycję **unpkg**.

* W obszarze **Biblioteka**wprowadź `@microsoft/signalr@latest`.

* Wybierz pozycję **Wybierz określone pliki**, rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *sygnalizujące. js* i *sygnalizujący. min. js*.

* Ustaw **lokalizację docelową** na plik *wwwroot/js/signaler/*, a następnie wybierz pozycję **Zainstaluj**.

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybór biblioteki](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan tworzy folder *wwwroot/js/sygnalizujący* i kopiuje do niego wybrane pliki.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta sygnalizującego za pomocą LibMan. Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry określają następujące opcje:
  * Użyj dostawcy unpkg.
  * Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .
  * Skopiuj tylko określone pliki.

  Dane wyjściowe wyglądają podobnie do poniższego przykładu:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W **terminalu**Uruchom następujące polecenie, aby zainstalować LibMan.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Przejdź do folderu projektu (taki, który zawiera plik *SignalRChat. csproj* ).

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta sygnalizującego za pomocą LibMan.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Parametry określają następujące opcje:
  * Użyj dostawcy unpkg.
  * Kopiuj pliki do lokalizacji *wwwroot/js/sygnalizującer* .
  * Skopiuj tylko określone pliki.

  Dane wyjściowe wyglądają podobnie do poniższego przykładu:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Tworzenie centrum sygnałów

*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.

* W folderze projektu SignalRChat Utwórz folder *Hubs* .

* W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  `ChatHub` Klasa dziedziczy od `Hub` klasy sygnalizującej. `Hub` Klasa zarządza połączeniami, grupami i obsługą wiadomości.

  `SendMessage` Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów. Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka. Kod sygnalizujący jest asynchroniczny, aby zapewnić maksymalną skalowalność.

## <a name="configure-signalr"></a>Konfigurowanie sygnalizującego

Serwer sygnalizujący musi być skonfigurowany tak, aby przekazywać żądania sygnałów do sygnalizującego.

* Dodaj następujący wyróżniony kod do pliku *Startup.cs* .

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Te zmiany umożliwiają dodanie sygnalizacji do ASP.NET Core systemów iniekcji i routingu.

## <a name="add-signalr-client-code"></a>Dodaj kod klienta sygnalizującego

* Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Powyższy kod ma następujące działanie:

  * Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.
  * Tworzy listę z `id="messagesList"` do wyświetlania komunikatów odbieranych z centrum sygnałów.
  * Zawiera odwołania do skryptów do programu Sygnalizującer i kod aplikacji *czatu. js* , który tworzysz w następnym kroku.

* W folderze *wwwroot/js* Utwórz plik *czatu. js* o następującym kodzie:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Powyższy kod ma następujące działanie:

  * Tworzy i uruchamia połączenie.
  * Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum.
  * Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.

## <a name="run-the-app"></a>Uruchomienie aplikacji

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* W zintegrowanym terminalu uruchom następujące polecenie:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz polecenie **uruchom > Uruchom bez debugowania**.

---

* Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

* Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .

  Nazwa i komunikat są natychmiast wyświetlane na obu stronach.

  ![Przykładowa aplikacja sygnalizująca](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu. Mogą pojawić się błędy związane z kodem HTML i JavaScript. Załóżmy na przykład, że umieścisz polecenie *signaler. js* w innym folderze niż skierowany. W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.
>   ![błąd podczas znajdowania sygnalizującer. js](signalr/_static/3.x/f12-console.png)
> * Jeśli zostanie wyświetlony komunikat o błędzie ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY w programie Chrome, uruchom następujące polecenia, aby zaktualizować certyfikat deweloperski:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym samouczku przedstawiono podstawy tworzenia aplikacji w czasie rzeczywistym przy użyciu usługi sygnalizującej. Omawiane kwestie:   

> [!div class="checklist"]  
> * Utwórz projekt sieci Web.   
> * Dodaj bibliotekę klienta sygnalizującego. 
> * Utwórz centrum sygnałów.   
> * Skonfiguruj projekt do używania sygnalizującego.   
> * Dodaj kod, który wysyła komunikaty z dowolnego klienta do wszystkich połączonych klientów.  
Na końcu będziesz mieć działającą aplikację czatu: ![Przykładowa aplikacja do obsługi sygnałów](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Wymagania wstępne    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Tworzenie projektu sieci Web 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Z menu wybierz pozycję **plik > nowy projekt**. 

* W oknie dialogowym **Nowy projekt** wybierz pozycję **zainstalowane > Visual C# > sieci Web > ASP.NET Core aplikacji sieci Web**. Nazwij projekt *SignalRChat*. 

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Wybierz pozycję **aplikacja sieci Web** , aby utworzyć projekt, który używa Razor Pages. 

* Wybierz platformę docelową programu **.NET Core**, wybierz pozycję **ASP.NET Core 2,2**, a następnie kliknij przycisk **OK**.    

  ![Okno dialogowe nowego projektu w programie Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) do folderu, w którym zostanie utworzony nowy folder projektu.  

* Uruchom następujące polecenia:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

* Z menu wybierz pozycję **plik > nowe rozwiązanie**.    

* Wybierz pozycję **.NET Core > app > ASP.NET Core Web App** (nie wybieraj **ASP.NET Core Web App (MVC)**).  

* Wybierz pozycję **Dalej**.  

* Nazwij projekt *SignalRChat*, a następnie wybierz pozycję **Utwórz**.   

--- 

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta sygnalizującego   

Biblioteka serwera sygnalizującego jest dołączona do `Microsoft.AspNetCore.App` pakietu. Biblioteka klienta JavaScript nie jest automatycznie dołączana do projektu. W tym samouczku użyjesz programu Library Manager (LibMan), aby uzyskać bibliotekę kliencką z *unpkg*. unpkg to usługa Content Delivery Network (CDN), która umożliwia dostarczanie elementów znalezionych w programie npm, w Menedżerze pakietów środowiska Node. js. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz polecenie **Dodaj** > **bibliotekę po stronie klienta**.  

* W oknie dialogowym **Dodawanie biblioteki po stronie klienta** dla **dostawcy** wybierz pozycję **unpkg**. 

* W polu **Biblioteka**wprowadź `@microsoft/signalr@3`i wybierz najnowszą wersję, która nie jest w wersji zapoznawczej.  

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — wybór biblioteki](signalr/_static/2.x/libman1.png)   

* Wybierz pozycję **Wybierz określone pliki**, rozwiń folder *dist/przeglądarka* , a następnie wybierz pozycję *sygnalizujące. js* i *sygnalizujący. min. js*. 

* Ustaw **lokalizację docelową** na plik *wwwroot/lib/sygnalizujący/*, a następnie wybierz pozycję **Zainstaluj**.    

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta — Wybieranie plików i lokalizacji docelowej](signalr/_static/2.x/libman2.png) 

  LibMan tworzy folder *wwwroot/lib/sygnalizujący* i kopiuje do niego wybrane pliki.    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* W zintegrowanym terminalu uruchom następujące polecenie, aby zainstalować LibMan.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta sygnalizującego za pomocą LibMan. Może być konieczne odczekanie kilku sekund przed wyświetleniem danych wyjściowych.   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry określają następujące opcje: 
  * Użyj dostawcy unpkg. 
  * Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .    
  * Skopiuj tylko określone pliki.  

  Dane wyjściowe wyglądają podobnie do poniższego przykładu:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)   

* W **terminalu**Uruchom następujące polecenie, aby zainstalować LibMan. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Przejdź do folderu projektu (taki, który zawiera plik *SignalRChat. csproj* ). 

* Uruchom następujące polecenie, aby uzyskać bibliotekę klienta sygnalizującego za pomocą LibMan.  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  Parametry określają następujące opcje: 
  * Użyj dostawcy unpkg. 
  * Kopiuj pliki do lokalizacji *wwwroot/lib/sygnalizującej* .    
  * Skopiuj tylko określone pliki.  

  Dane wyjściowe wyglądają podobnie do poniższego przykładu:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Tworzenie centrum sygnałów 

*Koncentrator* jest klasą, która służy jako potok wysokiego poziomu, który obsługuje komunikację klient-serwer.   

* W folderze projektu SignalRChat Utwórz folder *Hubs* .    

* W folderze *Hubs* utwórz plik *ChatHub.cs* o następującym kodzie: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  `ChatHub` Klasa dziedziczy od `Hub` klasy sygnalizującej. `Hub` Klasa zarządza połączeniami, grupami i obsługą wiadomości.    

  `SendMessage` Metoda może być wywoływana przez połączonego klienta w celu wysłania komunikatu do wszystkich klientów. Kod klienta JavaScript, który wywołuje metodę, jest wyświetlany w dalszej części samouczka. Kod sygnalizujący jest asynchroniczny, aby zapewnić maksymalną skalowalność.  

## <a name="configure-signalr"></a>Konfigurowanie sygnalizującego    

Serwer sygnalizujący musi być skonfigurowany tak, aby przekazywać żądania sygnałów do sygnalizującego.  

* Dodaj następujący wyróżniony kod do pliku *Startup.cs* .  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Te zmiany umożliwiają dodanie sygnału do ASP.NET Core systemu iniekcji zależności oraz potoku oprogramowania pośredniczącego.    

## <a name="add-signalr-client-code"></a>Dodaj kod klienta sygnalizującego  

* Zastąp zawartość w *Pages\Index.cshtml* następującym kodem:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Powyższy kod ma następujące działanie:   

  * Tworzy pola tekstowe dla nazwy i tekstu komunikatu oraz przycisk Prześlij.  
  * Tworzy listę z `id="messagesList"` do wyświetlania komunikatów odbieranych z centrum sygnałów. 
  * Zawiera odwołania do skryptów do programu Sygnalizującer i kod aplikacji *czatu. js* , który tworzysz w następnym kroku.  

* W folderze *wwwroot/js* Utwórz plik *czatu. js* o następującym kodzie:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Powyższy kod ma następujące działanie:   

  * Tworzy i uruchamia połączenie.    
  * Dodaje do przycisku Prześlij procedurę obsługi, która wysyła komunikaty do centrum. 
  * Dodaje do obiektu Connection program obsługi, który odbiera komunikaty z centrum i dodaje je do listy.  

## <a name="run-the-app"></a>Uruchomienie aplikacji  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Naciśnij **klawisze CTRL + F5** , aby uruchomić aplikację bez debugowania.   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* W zintegrowanym terminalu uruchom następujące polecenie:    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Z menu wybierz polecenie **uruchom > Uruchom bez debugowania**.

---

* Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

* Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij wiadomość** .  

  Nazwa i komunikat są natychmiast wyświetlane na obu stronach.   

  ![SignalRPrzykładowa aplikacja](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> Jeśli aplikacja nie działa, Otwórz narzędzia deweloperskie przeglądarki (F12) i przejdź do konsoli programu. Mogą pojawić się błędy związane z kodem HTML i JavaScript. Załóżmy na przykład, że umieścisz polecenie *signaler. js* w innym folderze niż skierowany. W takim przypadku odwołanie do tego pliku nie będzie działało i zobaczysz błąd 404 w konsoli.   
> ![błąd podczas znajdowania sygnalizującer. js](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Zasoby dodatkowe 
* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
