---
title: 'Samouczek: Wprowadzenie do stron Razor w ASP.NET Core'
author: rick-anderson
description: Ta seria samouczków pokazuje, jak używać stron Razor w ASP.NET Core. Dowiedz się, jak utworzyć model, wygenerować kod dla stron Razor, użyć Entity Framework Core i SQL Server do dostępu do danych, dodać funkcje wyszukiwania, dodać sprawdzanie poprawności danych i użyć migracji, aby zaktualizować model.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658544"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Samouczek: Wprowadzenie do stron Razor w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"
Jest to pierwszy samouczek z serii, który uczy podstaw tworzenia aplikacji sieci web ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji internetowej Razor Pages.
> * Uruchom aplikację.
> * Sprawdź pliki projektu.

Na końcu tego samouczka będziesz mieć działającą aplikację internetową Razor Pages, którą będziesz przeć w późniejszych samouczkach.

![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Tworzenie aplikacji internetowych ze stronami Razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* Utwórz nową ASP.NET podstawową aplikację sieci Web i wybierz pozycję **Dalej**.
  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2.1.png)
* Nazwij projekt **RazorPagesMovie**. Ważne jest, aby nadać projektowi *RazorPagesMovie,* aby obszary nazw były zgodne podczas kopiowania i wklejania kodu.
  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/config.png)

* Wybierz **ASP.NET Core 3.1** w menu **rozwijanym, Aplikacja sieci Web**, a następnie wybierz pozycję **Utwórz**.

![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/3/npx.png)

  Tworzony jest następujący projekt startowy:

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Zmień katalog (`cd`), który będzie zawierał projekt.

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *RazorPagesMovie.*
  * Polecenie `code` otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu programu Visual Studio Code.

* Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do kompilacji i debugowania brakuje "RazorPagesMovie". Dodać je?** Wybierz **pozycję Tak**.

  Katalog *.vscode* zawierający pliki *launch.json* i *tasks.json* jest dodawany do katalogu głównego projektu.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

![macOS Nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Wybierz pozycję **.NET Core** > **App** > **Web Application** > **Next**.

  ![macOS Nowe okno dialogowe projektu](razor-pages-start/_static/webapp.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** ustaw **platformę docelową** na **.NET Core 3.1**.

  ![Wybór systemu macOS .NET Core 3.1](razor-pages-start/_static/targetframework3.png)

* Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.

  ![nazwaproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uruchomienie aplikacji

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Oto omówienie głównych folderów projektu i plików, z którymi będziesz pracować w późniejszych samouczkach.

### <a name="pages-folder"></a>Folder Strony

Zawiera strony Razor i pliki pomocnicze. Każda strona Razor to para plików:

* Plik *cshtml zawierający* znaczniki HTML z kodem C# przy użyciu składni Razor.
* Plik *.cshtml.cs* zawierający kod C#, który obsługuje zdarzenia strony.

Pliki pomocnicze mają nazwy, które zaczynają się od podkreślenia. Na przykład plik *_Layout.cshtml* konfiguruje elementy interfejsu użytkownika wspólne dla wszystkich stron. Ten plik konfiguruje menu nawigacyjne w górnej części strony i informacje o prawach autorskich u dołu strony. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>folder wwwroot

Zawiera pliki statyczne, takie jak pliki HTML, Pliki JavaScript i pliki CSS. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Zawiera dane konfiguracyjne, takie jak parametry połączenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Zawiera punkt wejścia programu. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka z serii:

> [!div class="step-by-step"]
> [Dodawanie modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Jest to pierwszy samouczek z serii. [Seria](xref:tutorials/razor-pages/index) uczy podstaw budowania aplikacji internetowej ASP.NET Core Razor Pages.

[!INCLUDE[](~/includes/advancedRP.md)]

Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji internetowej Razor Pages.
> * Uruchom aplikację.
> * Sprawdź pliki projektu.

Na końcu tego samouczka będziesz mieć działającą aplikację internetową Razor Pages, którą będziesz przeć w późniejszych samouczkach.

![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a>Tworzenie aplikacji internetowych ze stronami Razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.

* Utwórz nową ASP.NET podstawową aplikację sieci Web i wybierz pozycję **Dalej**.

  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2.1.png)

* Nazwij projekt **RazorPagesMovie**. Ważne jest, aby nadać projektowi *RazorPagesMovie,* aby obszary nazw były zgodne podczas kopiowania i wklejania kodu.

  ![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/config.png)

* Wybierz **ASP.NET Core 2.2** w menu **rozwijanym, Aplikacja sieci Web**, a następnie wybierz pozycję **Utwórz**.

![nowa ASP.NET podstawowa aplikacja sieci Web](razor-pages-start/_static/np_2_2.2.png)

  Tworzony jest następujący projekt startowy:

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Zmień katalog (`cd`), który będzie zawierał projekt.

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *RazorPagesMovie.*
  * Polecenie `code` otwiera folder *RazorPagesMovie* w bieżącym wystąpieniu programu Visual Studio Code.

* Po pasku stanu OmniSharp płomień ikona zmieni kolor na zielony, okno dialogowe pyta **wymagane zasoby do kompilacji i debugowania brakuje "RazorPagesMovie". Dodać je?** Wybierz **pozycję Tak**.

  Katalog *.vscode* zawierający pliki *launch.json* i *tasks.json* jest dodawany do katalogu głównego projektu.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz **pozycję Plik** > **nowego rozwiązania**.

![macOS Nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* Wybierz pozycję **.NET Core** > **App** > **Web Application** > **Next**.

  ![macOS Nowe okno dialogowe projektu](razor-pages-start/_static/webapp.png)

* W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** ustaw **platformę docelową** na **.NET Core 3.1**.

  ![Wybór systemu macOS .NET Core 3.0](razor-pages-start/_static/targetframework3.png)

* Nazwij projekt **RazorPagesMovie**, a następnie wybierz pozycję **Utwórz**.

  ![nazwaproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uruchomienie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Naciśnij klawisze Ctrl+F5, aby uruchomić bez debugera.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Program Visual Studio uruchamia [program IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchamia aplikację. Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.

* Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2.png)

  Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Naciśnij **klawisze Ctrl-F5,** aby uruchomić bez debugera.

  Program Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do . Pasek adresu `localhost:port#` pokazuje, a `example.com`nie coś w stylu . To dlatego, `localhost` że jest standardową nazwa hosta dla komputera lokalnego. Localhost obsługuje tylko żądania sieci web z komputera lokalnego.

* Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2.png)

  Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Naciśnij **cmd-opt-F5,** aby uruchomić bez debugera.

  Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia `http://localhost:5001`przeglądarkę i przechodzi do .

* Na stronie głównej aplikacji wybierz pozycję **Zaakceptuj,** aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi danych osobowych, ale szablon projektu zawiera funkcję zgody w przypadku, gdy jest to potrzebne do przestrzegania ogólnego rozporządzenia o [ochronie danych (RODO)](xref:security/gdpr)Unii Europejskiej.

  ![Strona główna lub Indeks](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Na poniższej ilustracji przedstawiono aplikację po wyrażeniu zgody na śledzenie:

  ![Strona główna lub Indeks](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Oto omówienie głównych folderów projektu i plików, z którymi będziesz pracować w późniejszych samouczkach.

### <a name="pages-folder"></a>Folder Strony

Zawiera strony Razor i pliki pomocnicze. Każda strona Razor to para plików:

* Plik *cshtml zawierający* znaczniki HTML z kodem C# przy użyciu składni Razor.
* Plik *.cshtml.cs* zawierający kod C#, który obsługuje zdarzenia strony.

Pliki pomocnicze mają nazwy, które zaczynają się od podkreślenia. Na przykład plik *_Layout.cshtml* konfiguruje elementy interfejsu użytkownika wspólne dla wszystkich stron. Ten plik konfiguruje menu nawigacyjne w górnej części strony i informacje o prawach autorskich u dołu strony. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>folder wwwroot

Zawiera pliki statyczne, takie jak pliki HTML, Pliki JavaScript i pliki CSS. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Zawiera dane konfiguracyjne, takie jak parametry połączenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Zawiera punkt wejścia programu. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na pliki cookie. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wersja youtube tego samouczka](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka z serii:

> [!div class="step-by-step"]
> [Dodawanie modelu](xref:tutorials/razor-pages/model)

::: moniker-end
