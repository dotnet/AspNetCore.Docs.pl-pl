---
title: 'Samouczek: wprowadzenie do Razor stron w ASP.NET Core'
author: rick-anderson
description: Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia Razor aplikacji sieci web ASP.NET Core Pages.
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486229"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Samouczek: wprowadzenie do Razor stron w ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia Razor aplikacji sieci web ASP.NET Core Pages.

Aby uzyskać bardziej zaawansowane wprowadzenie do deweloperów, którzy znają kontrolery i widoki, zobacz [wprowadzenie do Razor stron](xref:razor-pages/index).

Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.  

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([jak pobrać](xref:index#how-to-download-a-sample)).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie Razor aplikacji sieci Web dla stron.
> * Uruchom aplikację.
> * Przejrzyj pliki projektu.

Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, którą można ulepszyć w kolejnych samouczkach.

![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Tworzenie Razor aplikacji sieci Web dla stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. Aby uzyskać więcej informacji, zobacz [Tworzenie nowego projektu w programie Visual Studio](/visualstudio/ide/create-new-project).

   ![Utwórz nowy projekt z okna uruchamiania](razor-pages-start/_static/5/start-window-create-new-project.png)

1. W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **ASP.NET Core aplikacja sieci Web**, a następnie wybierz przycisk **dalej**.

    ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/5/np.png)
    
1. W oknie dialogowym **Konfigurowanie nowego projektu** wprowadź wartość `RazorPagesMovie` w polu **Nazwa projektu**. Ważne jest, aby nazwa *Razor PagesMovie* projektu, w tym pasujące do wielkości liter, więc przestrzenie nazw są zgodne podczas kopiowania i wklejania przykładowego kodu.

1. Wybierz przycisk **Utwórz**.

    ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

1. W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz:
    1. **Platformy .NET Core** i **ASP.NET Core 5,0** na liście rozwijanej.
    1. **Aplikacja sieci Web**.
    1. **Create**.

     ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/5/npx.png)

    Tworzony jest następujący projekt początkowy:

    ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

1. Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.

1. Uruchom następujące polecenia:

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * `dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *Razor PagesMovie* .
   * `code`Polecenie otwiera folder *Razor PagesMovie* w bieżącym wystąpieniu Visual Studio Code.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Wybierz pozycję **plik** > **nowe rozwiązanie**.

    ![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **aplikacja internetowa aplikacji sieci Web** platformy .NET Core  >  . W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli** aplikacji  >    >  **sieci Web**  >  .

    ![Wybieranie szablonu aplikacji sieci Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

1. W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

    1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
    1. Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję programu .NET 5. x.
    1. Wybierz pozycję **Dalej**.

1. Nadaj projektowi nazwę *Razor PagesMovie* i wybierz pozycję **Utwórz**.

    ![macOS Nazwij projekt](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uruchamianie aplikacji

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.

### <a name="pages-folder"></a>Folder stron

Zawiera Razor strony i pliki pomocnicze. Każda Razor strona to para plików:

* Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# używając Razor składni.
* Plik *. cshtml.cs* , który ma kod C#, który obsługuje zdarzenia strony.

Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia. Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron. Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>folder wwwroot

Zawiera statyczne zasoby, takie jak pliki HTML, pliki JavaScript i pliki CSS. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

### appsettings.json

Zawiera dane konfiguracji, takie jak parametry połączenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Zawiera punkt wejścia dla aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Dalej: Dodawanie modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Jest to pierwszy samouczek dotyczący serii, który uczy się podstaw tworzenia Razor aplikacji sieci web ASP.NET Core Pages.

Aby uzyskać bardziej zaawansowane wprowadzenie do deweloperów, którzy znają kontrolery i widoki, zobacz [wprowadzenie do Razor stron](xref:razor-pages/index).

Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.  

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([jak pobrać](xref:index#how-to-download-a-sample)).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie Razor aplikacji sieci Web dla stron.
> * Uruchom aplikację.
> * Przejrzyj pliki projektu.

Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.

![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Tworzenie Razor aplikacji sieci Web dla stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.
* Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.
  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Nazwij projekt **Razor PagesMovie**. Ważne jest, aby nazwa projektu *Razor PagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.
  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

* Wybierz pozycję **ASP.NET Core 3,1** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Tworzony jest następujący projekt początkowy:

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *Razor PagesMovie* .
  * `code`Polecenie otwiera folder *Razor PagesMovie* w bieżącym wystąpieniu Visual Studio Code.

* Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " Razor PagesMovie". Dodać je?** Wybierz pozycję **Tak**.

  Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

  ![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **aplikacja internetowa aplikacji sieci Web** platformy .NET Core  >  . W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli** aplikacji  >    >  **sieci Web**  >  .

  ![Wybieranie szablonu aplikacji sieci Web macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 3. x.

  Wybierz pozycję **Dalej**.

* Nazwij projekt **Razor PagesMovie**, a następnie wybierz pozycję **Utwórz**.

  ![macOS Nazwij projekt](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uruchamianie aplikacji

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.

### <a name="pages-folder"></a>Folder stron

Zawiera Razor strony i pliki pomocnicze. Każda Razor strona to para plików:

* Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# używając Razor składni.
* Plik *. cshtml.cs* , który ma kod C#, który obsługuje zdarzenia strony.

Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia. Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron. Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>folder wwwroot

Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.jsna

Zawiera dane konfiguracji, takie jak parametry połączenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Zawiera punkt wejścia dla programu. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Dalej: Dodawanie modelu](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Jest to pierwszy samouczek dotyczący serii. [Seria](xref:tutorials/razor-pages/index) uczy się podstaw tworzenia Razor aplikacji sieci Web ASP.NET Core Pages.

Aby uzyskać bardziej zaawansowane wprowadzenie do deweloperów, którzy znają kontrolery i widoki, zobacz [wprowadzenie do Razor stron](xref:razor-pages/index).

Na końcu serii będziesz mieć aplikację, która zarządza bazą danych filmów.  

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([jak pobrać](xref:index#how-to-download-a-sample)).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie Razor aplikacji sieci Web dla stron.
> * Uruchom aplikację.
> * Przejrzyj pliki projektu.

Na końcu tego samouczka będziesz mieć Razor aplikację sieci Web strony roboczej, która zostanie utworzona w kolejnych samouczkach.

![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Tworzenie Razor aplikacji sieci Web dla stron

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.

* Utwórz nową aplikację sieci Web ASP.NET Core a następnie wybierz pozycję **dalej**.

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Nazwij projekt **Razor PagesMovie**. Ważne jest, aby nazwa projektu *Razor PagesMovie* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.

  ![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/config.png)

* Wybierz pozycję **ASP.NET Core 2,2** na liście rozwijanej, **aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.

![Nowa aplikacja sieci Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Tworzony jest następujący projekt początkowy:

  ![Eksplorator rozwiązań](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Przejdź do katalogu ( `cd` ), który będzie zawierać projekt.

* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *Razor PagesMovie* .
  * `code`Polecenie otwiera folder *Razor PagesMovie* w bieżącym wystąpieniu Visual Studio Code.

* Gdy ikona płomienia OmniSharp na pasku stanu zmieni kolor na zielony, w oknie dialogowym zostanie wyświetlony monit **o podanie wymaganych zasobów do skompilowania i debugowania z elementu " Razor PagesMovie". Dodać je?** Wybierz pozycję **Tak**.

  Katalog *. programu vscode* , zawierający *launch.json* i *tasks.jsdla* plików, jest dodawany do katalogu głównego projektu.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Wybierz pozycję **plik** > **nowe rozwiązanie**.

![macOS nowe rozwiązanie](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **aplikacja internetowa aplikacji sieci Web** platformy .NET Core  >  . W wersji 8,6 lub nowszej wybierz kolejno pozycje **Sieć Web i aplikacja konsoli** aplikacji  >    >  **sieci Web**  >  .

* W oknie dialogowym **Konfigurowanie nowej aplikacji sieci Web** :

  * Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.
  * Jeśli jest prezentowana opcja wyboru **platformy docelowej**, wybierz najnowszą wersję 2. x.

  Wybierz pozycję **Dalej**.

* Nazwij projekt **Razor PagesMovie**, a następnie wybierz pozycję **Utwórz**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Uruchamianie aplikacji

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

  Uruchamianie aplikacji za pomocą <kbd>klawiszy CTRL + F5</kbd> (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację. Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.

* Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Naciśnij <kbd>klawisze CTRL + F5</kbd> , aby uruchomić bez debugera.

  Uruchamianie aplikacji za pomocą <kbd>klawiszy CTRL + F5</kbd> (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.

  Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` . Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

* Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2.png)

  Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Naciśnij **polecenie cmd-opt-F5** , aby uruchomić program bez debugera.

  Uruchamianie aplikacji za pomocą <kbd>polecenia CMD + OPT + F5</kbd> (tryb bez debugowania) umożliwia wprowadzanie zmian w kodzie, zapisywanie pliku, odświeżanie przeglądarki i wyświetlanie zmian w kodzie. Wielu deweloperów woli używać trybu bez debugowania, aby móc szybko uruchomić aplikację i wyświetlić zmiany.

  Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` .

* Na stronie głównej aplikacji wybierz pozycję **Akceptuj** , aby wyrazić zgodę na śledzenie.

  Ta aplikacja nie śledzi informacji osobistych, ale szablon projektu zawiera funkcję zgody na wypadek, gdyby była niezbędna do przestrzegania Ogólne rozporządzenie o ochronie danych Unii Europejskiej [(Rodo)](xref:security/gdpr).

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Na poniższej ilustracji przedstawiono aplikację po udzieleniu zgody na śledzenie:

  ![Strona główna lub:: No-Loc (index)::: Page](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Poniżej przedstawiono Omówienie folderów i plików projektu głównego, z których będziesz korzystać w kolejnych samouczkach.

### <a name="pages-folder"></a>Folder stron

Zawiera Razor strony i pliki pomocnicze. Każda Razor strona to para plików:

* Plik *. cshtml* , który zawiera znaczniki HTML z kodem C# używając Razor składni.
* Plik *. cshtml.cs* , który ma kod C#, który obsługuje zdarzenia strony.

Pliki pomocnicze mają nazwy zaczynające się od znaku podkreślenia. Na przykład plik *_Layout. cshtml* służy do konfigurowania elementów interfejsu użytkownika wspólnych dla wszystkich stron. Ten plik konfiguruje menu nawigacji w górnej części strony i informacje o prawach autorskich w dolnej części strony. Aby uzyskać więcej informacji, zobacz <xref:mvc/views/layout>.

Razor Strony pochodzą od `PageModel` . Zgodnie z Konwencją `PageModel` Klasa pochodna ma nazwę `<PageName>Model` .

### <a name="wwwroot-folder"></a>folder wwwroot

Zawiera pliki statyczne, takie jak pliki HTML, pliki JavaScript i pliki CSS. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.jsna

Zawiera dane konfiguracji, takie jak parametry połączenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Zawiera punkt wejścia dla programu. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Zawiera kod, który konfiguruje zachowanie aplikacji, na przykład czy wymaga zgody na polecenie cookie s. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Wersja tego samouczka usługi YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Następne kroki

> [!div class="step-by-step"]
> [Dalej: Dodawanie modelu](xref:tutorials/razor-pages/model)

::: moniker-end
