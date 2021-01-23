---
title: Wprowadzenie do ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: aaf930eee351ed757be60f648bce88b182d52799
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710797"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Wprowadzenie do ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Jest to pierwszy samouczek serii, który uczy się ASP.NET Core tworzenia aplikacji sieci Web MVC z kontrolerami i widokami.

Na końcu serii będziesz mieć aplikację, która zarządza i wyświetla dane filmu. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodawanie i tworzenie szkieletu modelu.
> * Pracuj z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
* W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.
* W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `MvcMovie` w polu **Nazwa projektu**. Ważne jest, aby nazwa projektu *MvcMovie*. Wielkie litery muszą pasować do każdego `namespace` dopasowania, gdy kod jest kopiowany.
* Wybierz przycisk **Utwórz**.
* W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:
  * **Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.
  * **ASP.NET Core aplikacji sieci Web (Model-View-Controller)**.
  * **Create**.

![Tworzenie nowej aplikacji sieci Web ASP.NET Core ](start-mvc/_static/mvcVS19v16.9.png)

Aby zapoznać się z alternatywnymi metodami tworzenia projektu, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).

Program Visual Studio użył domyślnego szablonu projektu dla utworzonego projektu MVC. Utworzony projekt:

* Jest działającą aplikacją.
* To podstawowy projekt startowy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W samouczku założono znajomość VS Code. Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help).

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Jeśli zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do skompilowania i debugowania nie ma w "MvcMovie". Dodać je?**, wybierz pozycję **tak** .

  * `dotnet new mvc -o MvcMovie`: Tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**. W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli zostanie wyświetlona opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 5. x.
  * Wybierz opcję **Dalej**.

* Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugera.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Uruchamia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Uruchamia aplikację.

  Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.

Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:

* Wprowadzanie zmian w kodzie.
* Zapisz plik.
* Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.

Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :

![Menu Debuguj](start-mvc/_static/debug_menu50.png)

Możesz debugować aplikację, wybierając przycisk **IIS Express**

![IIS Express](start-mvc/_static/iis_express50.png)

Na poniższej ilustracji przedstawiono aplikację:

![Strona główna lub indeks](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Wybierz kombinację klawiszy CTRL + F5, aby uruchomić bez debugera.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Uruchamia [Kestrel](xref:fundamentals/servers/kestrel)
  * Uruchamia przeglądarkę.
  * Nawiguje do `https://localhost:5001` .

  Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:

* Wprowadzanie zmian w kodzie.
* Zapisz plik.
* Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.

  ![Strona główna lub indeks](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację.

  Visual Studio dla komputerów Mac:

  * Uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) .
  * Uruchamia przeglądarkę.
  * Nawiguje do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.

  [!INCLUDE[](~/includes/trustCertMac.md)]

  Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.

Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).

Na poniższej ilustracji przedstawiono aplikację:

![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.

> [!div class="step-by-step"]
> [Dalej: dodawanie kontrolera](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Jest to pierwszy samouczek serii, który uczy się ASP.NET Core tworzenia aplikacji sieci Web MVC z kontrolerami i widokami.

Na końcu serii będziesz mieć aplikację, która zarządza i wyświetla dane filmu. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodawanie i tworzenie szkieletu modelu.
> * Pracuj z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.

* Wybierz pozycję **ASP.NET Core aplikacja sieci Web** > **dalej**.

  ![Utwórz nowy projekt aplikacji sieci Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* Nadaj projektowi nazwę **MvcMovie** i wybierz pozycję **Utwórz**. Ważne jest, aby nazwa **MvcMovie** projektu była taka sama, jak w przypadku kopiowania kodu, przestrzeń nazw będzie pasować.

  ![Skonfiguruj nowy projekt](start-mvc/_static/config.png)

* Wybierz pozycję **aplikacja sieci Web (Model-View-Controller)**. Z listy rozwijanej wybierz pozycję **.NET Core** i **ASP.NET Core 3,1**, a następnie wybierz pozycję **Utwórz**.

  ![Okno dialogowe Nowy projekt, .NET Core w lewym okienku, ASP.NET Core Web ](start-mvc/_static/new_project30.png)

Program Visual Studio użył domyślnego szablonu projektu dla utworzonego projektu MVC. Utworzony projekt:

* Jest działającą aplikacją.
* To podstawowy projekt startowy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

W samouczku założono znajomość VS Code. Aby uzyskać więcej informacji, zobacz [wprowadzenie do vs Code](https://code.visualstudio.com/docs) i [Visual Studio Code pomocy](#visual-studio-code-help).

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalog ( `cd` ) do folderu, który będzie zawierać projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "MvcMovie". Dodać je?**, wybierz pozycję **tak**.

  * `dotnet new mvc -o MvcMovie`: Tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie* .
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie. csproj* w Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >  aplikacja sieci Web **aplikacji**.NET Core  >  **(Model-View-Controller)**  >  **dalej**. W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli**  >    >  **aplikacji sieci Web (Model-View-Controller)**  >  **dalej**.

  ![Wybieranie szablonu aplikacji sieci Web macOS](start-mvc/_static/web_app_template_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli zostanie wyświetlona opcja wyboru **platformy docelowej** , wybierz najnowszą wersję 3. x.
  * Wybierz opcję **Dalej**.

* Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.

  ![macOS Nazwij projekt](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugowania.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio:

  * Uruchamia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).
  * Uruchamia aplikację.

  Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.

Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:

* Wprowadzanie zmian w kodzie.
* Zapisz plik.
* Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.

Możesz uruchomić aplikację w trybie debugowania lub bez debugowania z elementu menu **Debuguj** :

![Menu Debuguj](start-mvc/_static/debug_menu.png)

Możesz debugować aplikację, wybierając przycisk **IIS Express**

![IIS Express](start-mvc/_static/iis_express.png)

Na poniższej ilustracji przedstawiono aplikację:

![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Wybierz kombinację klawiszy CTRL + F5, aby uruchomić aplikację bez debugowania.

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code:

  * Uruchamia [Kestrel](xref:fundamentals/servers/kestrel)
  * Uruchamia przeglądarkę.
  * Nawiguje do `https://localhost:5001` .

  Na pasku adresu są wyświetlane inne elementy `localhost:port:5001` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

Uruchamianie aplikacji bez debugowania poprzez wybranie kombinacji klawiszy CTRL + F5 umożliwia:

* Wprowadzanie zmian w kodzie.
* Zapisz plik.
* Szybko Odśwież przeglądarkę i Sprawdź zmiany w kodzie.

  ![Strona główna lub indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **Uruchom** > **Uruchom bez debugowania** , aby uruchomić aplikację.

  Visual Studio dla komputerów Mac: uruchamia serwer [Kestrel](xref:fundamentals/servers/index#kestrel) , uruchamia przeglądarkę i przechodzi do `http://localhost:port` , gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Standardowa nazwa hosta dla komputera lokalnego to `localhost` . Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.

Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Run (uruchamianie** ).

Na poniższej ilustracji przedstawiono aplikację:

![Strona główna lub indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka znajdziesz informacje na temat MVC i zacznij pisać kod.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end
