---
title: Rozpoczęcie pracy z ASP.NET Core MVC
author: rick-anderson
description: Dowiedz się, jak rozpocząć pracę z ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662478"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Rozpoczęcie pracy z ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ten samouczek uczy podstaw tworzenia aplikacji sieci web ASP.NET Core MVC.

Aplikacja zarządza bazą danych tytułów filmów. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodaj i rusztowanie modelu.
> * Praca z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.

* Wybierz **ASP.NET Podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.

![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/np_2.1.png)

* Nazwij projekt **MvcMovie** i wybierz pozycję **Utwórz**. Ważne jest, aby nadać nazwę projektowi **MvcMovie,** więc podczas kopiowania kodu obszar nazw będzie zgodny.

  ![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/config.png)

* Wybierz **pozycję Aplikacja sieci Web(Kontroler widoku modelu),** a następnie wybierz pozycję **Utwórz**.

![Nowe okno dialogowe projektu ,NET Core w lewym okienku, ASP.NET sieć Web Core ](start-mvc/_static/new_project30.png)

Program Visual Studio użył domyślnego szablonu dla właśnie utworzonego projektu MVC. Masz teraz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji. Jest to podstawowy projekt startowy.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Samouczek zakłada familarity z VS Code. Aby uzyskać więcej [informacji,](https://code.visualstudio.com/docs) zobacz Wprowadzenie do programu VS Code i visual [studio code.](#visual-studio-code-help)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * W oknie dialogowym z **wymaganymi zasobami do tworzenia i debugowania brakuje w "MvcMovie". Dodać je?**  Wybierz **tak**

  * `dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie.*
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie.csproj* w programie Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

  ![macOS Nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* Wybierz opcję **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Dalej**.

  ![macOS Nowe okno dialogowe projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** ustaw **docelową platformę** **programu .NET Core 3.1**.

  ![Wybór systemu macOS .NET Core 3.1](./start-mvc/_static/new_project_31_vsmac.png)

* Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.

---

### <a name="run-the-app"></a>Uruchomienie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wybierz **klawisz Ctrl-F5,** aby uruchomić aplikację w trybie bez debugowania.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację. Zwróć uwagę, że `localhost:port#` pasek adresu `example.com`pokazuje, a nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
* Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z elementu menu **Debugowania:**

  ![Menu debugowania](start-mvc/_static/debug_menu.png)

* Aplikację można debugować, wybierając przycisk **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `https://localhost:5001`przeglądarkę i przechodzi do . Pasek adresu `localhost:port:5001` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego.

  Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby odświeżyć stronę i wyświetlić zmiany.

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz **uruchom** > **start bez debugowania,** aby uruchomić aplikację. Program Visual Studio dla komputerów Mac uruchamia serwer [Kestrel,](xref:fundamentals/servers/index#kestrel) uruchamia przeglądarkę i przechodzi do `http://localhost:port`, gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Uruchom.**

  Na poniższej ilustracji przedstawiono aplikację:

  ![Strona główna lub Indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka dowiesz się o MVC i rozpocząć pisanie kodu.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

Ten samouczek uczy podstaw tworzenia aplikacji sieci web ASP.NET Core MVC.

Aplikacja zarządza bazą danych tytułów filmów. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz aplikację internetową.
> * Dodaj i rusztowanie modelu.
> * Praca z bazą danych.
> * Dodaj wyszukiwanie i sprawdzanie poprawności.

Na końcu masz aplikację, która może zarządzać i wyświetlać dane filmu.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.

* Wybierz **ASP.NET Podstawową aplikację sieci Web,** a następnie wybierz pozycję **Dalej**.

![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/np_2.1.png)

* Nazwij projekt **MvcMovie** i wybierz pozycję **Utwórz**. Ważne jest, aby nadać nazwę projektowi **MvcMovie,** więc podczas kopiowania kodu obszar nazw będzie zgodny.

  ![nowa ASP.NET podstawowa aplikacja sieci Web](start-mvc/_static/config.png)


* Wybierz **pozycję Aplikacja sieci Web(Kontroler widoku modelu),** a następnie wybierz pozycję **Utwórz**.

![Nowe okno dialogowe projektu ,NET Core w lewym okienku, ASP.NET sieć Web Core ](start-mvc/_static/new_project22-21.png)

Program Visual Studio użył domyślnego szablonu dla właśnie utworzonego projektu MVC. Masz teraz działającą aplikację, wprowadzając nazwę projektu i wybierając kilka opcji. Jest to podstawowy projekt startowy i jest to dobre miejsce do rozpoczęcia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Samouczek zakłada familarity z VS Code. Aby uzyskać więcej [informacji,](https://code.visualstudio.com/docs) zobacz Wprowadzenie do programu VS Code i visual [studio code.](#visual-studio-code-help)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.
* Uruchom następujące polecenie:

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * W oknie dialogowym z **wymaganymi zasobami do tworzenia i debugowania brakuje w "MvcMovie". Dodać je?**  Wybierz **tak**

  * `dotnet new mvc -o MvcMovie`: tworzy nowy projekt ASP.NET Core MVC w folderze *MvcMovie.*
  * `code -r MvcMovie`: Ładuje plik projektu *MvcMovie.csproj* w programie Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

  ![macOS Nowe rozwiązanie](./start-mvc/_static/new_project_vsmac.png)

* Wybierz opcję **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Dalej**.

  ![macOS Nowe okno dialogowe projektu](./start-mvc/_static/new_project_mvc_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** zaakceptuj domyślną **platformę docelową** **programu .NET Core 2.2**.

  ![Wybór systemu macOS .NET Core 2.2](./start-mvc/_static/new_project_22_vsmac.png)

* Nazwij projekt **MvcMovie**, a następnie wybierz pozycję **Utwórz**.

---

### <a name="run-the-app"></a>Uruchomienie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Wybierz **klawisz Ctrl-F5,** aby uruchomić aplikację w trybie bez debugowania.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację. Zwróć uwagę, że `localhost:port#` pasek adresu `example.com`pokazuje, a nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
* Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z elementu menu **Debugowania:**

  ![Menu debugowania](start-mvc/_static/debug_menu.png)

* Aplikację można debugować, wybierając przycisk **IIS Express**

  ![IIS Express](start-mvc/_static/iis_express.png)

* Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)

  ![Strona główna lub Indeks](start-mvc/_static/privacy.png)

  Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `https://localhost:5001`przeglądarkę i przechodzi do . Pasek adresu `localhost:port:5001` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego.

  Uruchomienie aplikacji za pomocą klawiszy Ctrl+F5 (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby odświeżyć stronę i wyświetlić zmiany.

* Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)

  ![Strona główna lub Indeks](start-mvc/_static/privacy.png)

  Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:

  ![Strona główna lub Indeks](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Wybierz **uruchom** > **start bez debugowania,** aby uruchomić aplikację. Program Visual Studio dla komputerów Mac uruchamia serwer [Kestrel,](xref:fundamentals/servers/index#kestrel) uruchamia przeglądarkę i przechodzi do `http://localhost:port`, gdzie *port* jest losowo wybranym numerem portu.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port. Po uruchomieniu aplikacji zobaczysz inny numer portu.
* Aplikację można uruchomić w trybie debugowania lub bez debugowania z menu **Uruchom.**

* Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę na śledzenie. Ta aplikacja nie śledzi informacji osobistych. Wygenerowany szablon kod zawiera zasoby ułatwiające spełnienie [ogólnego rozporządzenia o ochronie danych (RODO).](xref:security/gdpr)

  ![Strona główna lub Indeks](./start-mvc/_static/output_privacy_macos.png)

  Na poniższej ilustracji przedstawiono aplikację po zaakceptowaniu śledzenia:

  ![Strona główna lub Indeks](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

W następnej części tego samouczka dowiesz się o MVC i rozpocząć pisanie kodu.

> [!div class="step-by-step"]
> [Dalej](adding-controller.md)

::: moniker-end
