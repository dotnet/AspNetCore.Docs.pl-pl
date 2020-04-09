---
title: Używanie ASP.NET Core SignalR z typescriptem i pakietem internetowym
author: ssougnez
description: W tym samouczku można skonfigurować pakiet Webpack do SignalR wiązki i tworzenia aplikacji sieci web ASP.NET Core, której klient jest napisany w języku TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511343"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Użyj ASP.NET Core SignalR z TypeScript i Webpack

Przez [Sébastien Sougnez](https://twitter.com/ssougnez) i [Scott Addie](https://twitter.com/Scott_Addie)

[Pakiet internetowy](https://webpack.js.org/) umożliwia deweloperom wiązać i tworzyć zasoby po stronie klienta aplikacji sieci web. Ten samouczek pokazuje, przy użyciu Webpack w ASP.NET Core SignalR aplikacji internetowej, której klient jest napisany w [języku TypeScript](https://www.typescriptlang.org/).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rusztowanie aplikacji Core SignalR ASP.NET starter
> * Konfigurowanie klienta SignalR TypeScript
> * Konfigurowanie potoku kompilacji przy użyciu pakietu Webpack
> * Konfigurowanie serwera SignalR
> * Włączanie komunikacji między klientem a serwerem

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**
* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [C# dla programu Visual Studio Code w wersji 1.17.1 lub nowszej](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Tworzenie aplikacji sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Skonfiguruj program Visual Studio, aby wyszukywać npm w zmiennej środowiskowej *PATH.* Domyślnie program Visual Studio używa wersji npm znalezionej w katalogu instalacyjnym. Postępuj zgodnie z poniższymi instrukcjami w programie Visual Studio:

1. Uruchom program Visual Studio. W oknie startowym wybierz pozycję **Kontynuuj bez kodu**.
1. Przejdź do **pozycji** > **Opcje** > narzędzi Projekty i rozwiązania **Zarządzania pakietami** > **Projects and Solutions** > sieci Web Zewnętrzne narzędzia sieci **Web**.
1. Wybierz wpis *$(PATH)* z listy. Kliknij strzałkę w górę, aby przenieść wpis na drugą pozycję na liście, a następnie wybierz **przycisk OK**.

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfiguracja programu Visual Studio została ukończona.

1. Użyj opcji menu **Plik** > **nowego** > **projektu** i wybierz szablon ASP.NET Core **Web Application.** Wybierz **pozycję Dalej**.
1. Nazwij projekt *SignalRWebPack*i wybierz pozycję **Utwórz**.
1. Wybierz *.NET Core* z listy rozwijanej struktury docelowej i wybierz *ASP.NET Core 3.1* z listy rozwijanej selektora platformy. Zaznacz **pusty** szablon i wybierz pozycję **Utwórz**.

Dodaj `Microsoft.TypeScript.MSBuild` pakiet do projektu:

1. W **Eksploratorze rozwiązań** (prawe okienko) kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Zarządzaj pakietami NuGet**. Na karcie **Przeglądaj** wyszukaj `Microsoft.TypeScript.MSBuild`, a następnie kliknij pozycję **Zainstaluj** po prawej stronie, aby zainstalować pakiet.

Visual Studio dodaje pakiet NuGet w węźle **Zależności** w **Eksploratorze rozwiązań,** włączając kompilację TypeScript w projekcie.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **terminalu zintegrowanym:**

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* Polecenie `dotnet new` tworzy pustą aplikację sieci web ASP.NET Core w katalogu *SignalRWebPack.*
* Polecenie `code` otwiera folder *SignalRWebPack* w bieżącym wystąpieniu programu Visual Studio Code.

Uruchom następujące polecenie .NET Core CLI w **terminalu zintegrowanym:**

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Poprzednie polecenie dodaje pakiet [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) włączając kompilację TypeScript w projekcie.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurowanie pakietu Web i kodu TypeScript

Poniższe kroki konfigurują konwersję kodu TypeScript na język JavaScript i łączenie zasobów po stronie klienta.

1. Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *package.json:*

    ```console
    npm init -y
    ```

1. Dodaj wyróżnioną właściwość do pliku *package.json* i zapisz zmiany pliku:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Ustawienie `private` właściwości, `true` aby zapobiec ostrzeżenia instalacji pakietu w następnym kroku.

1. Zainstaluj wymagane pakiety npm. Uruchom następujące polecenie z poziomu głównego projektu:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Niektóre szczegóły polecenia, aby pamiętać:

    * Numer wersji jest `@` zgodny ze znakiem dla każdej nazwy pakietu. npm instaluje te konkretne wersje pakietów.
    * Opcja `-E` wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [przechowywania wersji semantycznych](https://semver.org/) do *pliku package.json*. Na przykład, `"webpack": "4.41.5"` jest używany `"webpack": "^4.41.5"`zamiast . Ta opcja zapobiega niezamierzonej aktualizacji do nowszych wersji pakietu.

    Aby uzyskać więcej informacji, zobacz dokumenty [npm-install.](https://docs.npmjs.com/cli/install)

1. Zastąp `scripts` właściwość pliku *package.json* następującym kodem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Niektóre wyjaśnienie skryptów:

    * `build`: Łączy zasoby po stronie klienta w trybie rozwoju i obserwuje zmiany w plikach. Obserwator plików powoduje, że pakiet do ponownego generowania za każdym razem, gdy zmienia się plik projektu. Opcja `mode` wyłącza optymalizacje produkcji, takie jak potrząsanie drzewem i minyfikacja. Używaj `build` tylko w rozwoju.
    * `release`: Łączy zasoby po stronie klienta w trybie produkcyjnym.
    * `publish`: Uruchamia `release` skrypt w celu wiązania zasobów po stronie klienta w trybie produkcyjnym. Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejsu wiersza polecenia .NET Core, aby opublikować aplikację.

1. Utwórz plik o nazwie *webpack.config.js*, w katalogu głównym projektu, z następującym kodem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Poprzedni plik konfiguruje kompilację webpacku. Niektóre szczegóły konfiguracji, aby pamiętać:

    * Właściwość `output` zastępuje domyślną wartość *dist*. Pakiet jest zamiast emitowane w katalogu *wwwroot.*
    * Tablica `resolve.extensions` zawiera *plik .js* do importowania javascript klienta SignalR.

1. Utwórz nowy katalog *src* w katalogu głównym projektu, aby przechowywać zasoby po stronie klienta projektu.

1. Utwórz *src/index.html* za pomocą następujących znaczników.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Poprzedni kod HTML definiuje znaczniki standardowego strony głównej.

1. Utwórz nowy katalog *src/css.* Jego celem jest przechowywanie plików *.css* projektu.

1. Utwórz *src/css/main.css* z następującymi CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Poprzedni plik *main.css* stylizuje aplikację.

1. Utwórz *src/tsconfig.json* z następującym JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Powyższy kod konfiguruje kompilator TypeScript do tworzenia kodu JavaScript zgodnego z [kodem ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Utwórz *src/index.ts* z następującym kodem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:

    * `keyup`: To zdarzenie jest uruchamiane, gdy użytkownik wpisuje pole tekstowe. `tbMessage` Funkcja `send` jest wywoływana, gdy użytkownik naciśnie klawisz **Enter.**
    * `click`: To zdarzenie jest uruchamiane, gdy użytkownik kliknie przycisk **Wyślij.** Wywołanie funkcji `send`.

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

1. W `Startup.Configure`, dodaj wywołania [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Powyższy kod umożliwia serwerowi zlokalizowanie i obsługa pliku *index.html.*  Plik jest obsługiwany, czy użytkownik wprowadzi pełny adres URL lub główny adres URL aplikacji internetowej.

1. Na końcu `Startup.Configure`, map */hub* trasy `ChatHub` do koncentratora. Zastąp kod, który wyświetla *Hello World!* z następującą linią: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. W `Startup.ConfigureServices`, zadzwoń [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Utwórz nowy katalog o nazwie *Hubs* w katalogu głównym projektu *SignalRWebPack/* do przechowywania centrum SignalR.

1. Utwórz centrum *hubs/ChatHub.cs* z następującym kodem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Dodaj następującą `using` instrukcję w górnej części pliku *Startup.cs,* aby rozwiązać `ChatHub` odwołanie:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Włączanie komunikacji z klientem i serwerem

Aplikacja wyświetla obecnie podstawowy formularz do wysyłania wiadomości, ale nie jest jeszcze funkcjonalny. Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi wiadomościami.

1. Uruchom następujące polecenie w katalogu głównym projektu:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Poprzednie polecenie instaluje:

     * [Klient SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie wiadomości do serwera.
     * Definicje typów TypeScript dla node.js, który umożliwia sprawdzanie w czasie kompilacji typów Node.js.

1. Dodaj wyróżniony kod do pliku *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Poprzedni kod obsługuje odbieranie wiadomości z serwera. Klasa `HubConnectionBuilder` tworzy nowego konstruktora do konfigurowania połączenia z serwerem. Funkcja `withUrl` konfiguruje adres URL centrum.

    SignalR umożliwia wymianę wiadomości między klientem a serwerem. Każda wiadomość ma określoną nazwę. Na przykład wiadomości o `messageReceived` nazwie można uruchomić logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie wiadomości. Nasłuchiwanie określonej wiadomości `on` można wykonać za pomocą funkcji. Można odsłuchać dowolną liczbę nazw wiadomości. Możliwe jest również przekazywanie parametrów do wiadomości, takich jak imię i nazwisko autora oraz zawartość odebranej wiadomości. Po odebraniu wiadomości przez `div` klienta tworzony jest nowy element z nazwą `innerHTML` autora i zawartością wiadomości w jego atrybucie. Jest dodawany do `div` głównego elementu wyświetlającego komunikaty.

1. Teraz, gdy klient może odbierać wiadomość, skonfiguruj ją do wysyłania wiadomości. Dodaj wyróżniony kod do pliku *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Wysyłanie wiadomości za pośrednictwem połączenia WebSockets wymaga wywołania `send` metody. Pierwszym parametrem metody jest nazwa wiadomości. Dane wiadomości zamieszkuje inne parametry. W tym przykładzie komunikat `newMessage` zidentyfikowany jako jest wysyłany do serwera. Wiadomość składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego. Jeśli wiadomość działa, wartość pola tekstowego jest wyczyszczona.

1. Dodaj metodę `NewMessage` do klasy `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Poprzedni kod emituje odebrane wiadomości do wszystkich połączonych użytkowników po serwer otrzyma je. Nie ma potrzeby, aby `on` mieć ogólną metodę, aby odbierać wszystkie wiadomości. Metoda nazwana nazwą wiadomości wystarczy.

    W tym przykładzie klient TypeScript wysyła `newMessage`wiadomość zidentyfikowaną jako . Metoda C# `NewMessage` oczekuje danych wysyłanych przez klienta. Połączenie jest nawiązywać do [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Odebrane wiadomości są wysyłane do wszystkich klientów połączonych z koncentratorem.

## <a name="test-the-app"></a>Testowanie aplikacji

Upewnij się, że aplikacja działa z następującymi krokami.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Uruchom pakiet Webpack w trybie *wydania.* Korzystając z okna **Konsola Menedżera pakietów,** uruchom następujące polecenie w katalogu głównym projektu. Jeśli nie znajdujesz się w `cd SignalRWebPack` katalogu głównym projektu, wprowadź przed wprowadzeniem polecenia.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację w przeglądarce bez dołączania debugera. Plik *wwwroot/index.html* jest obsługiwany w `http://localhost:<port_number>`pliku .

   Jeśli zostanie wyświetlonych błędów kompilacji, spróbuj zamknąć i ponownie otworzyć rozwiązanie. 

1. Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę). Wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.** Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom pakiet Webpack w trybie *wydania,* wykonując następujące polecenie w katalogu głównym projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:

    ```dotnetcli
    dotnet run
    ```

    Serwer sieci web uruchamia aplikację i udostępnia ją na localhost.

1. Otwórz przeglądarkę, aby . `http://localhost:<port_number>` Zostanie podany plik *wwwroot/index.html.* Skopiuj adres URL z paska adresu.

1. Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę). Wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.** Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**
* [.NET Core SDK 2.2 lub nowsza](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 lub nowsza](https://dotnet.microsoft.com/download/dotnet-core)
* [C# dla programu Visual Studio Code w wersji 1.17.1 lub nowszej](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Tworzenie aplikacji sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Skonfiguruj program Visual Studio, aby wyszukywać npm w zmiennej środowiskowej *PATH.* Domyślnie program Visual Studio używa wersji npm znalezionej w katalogu instalacyjnym. Postępuj zgodnie z poniższymi instrukcjami w programie Visual Studio:

1. Przejdź do **pozycji** > **Opcje** > narzędzi Projekty i rozwiązania **Zarządzania pakietami** > **Projects and Solutions** > sieci Web Zewnętrzne narzędzia sieci **Web**.
1. Wybierz wpis *$(PATH)* z listy. Kliknij strzałkę w górę, aby przenieść wpis na drugą pozycję na liście.

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfiguracja programu Visual Studio została ukończona. Nadszedł czas, aby utworzyć projekt.

1. Użyj opcji menu **Plik** > **nowego** > **projektu** i wybierz szablon ASP.NET Core **Web Application.**
1. Nazwij projekt *SignalRWebPack*i wybierz pozycję **Utwórz**.
1. Wybierz *.NET Core* z listy rozwijanej struktury docelowej i wybierz *ASP.NET Core 2.2* z listy rozwijanej selektora platformy. Zaznacz **pusty** szablon i wybierz pozycję **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **terminalu zintegrowanym:**

```dotnetcli
dotnet new web -o SignalRWebPack
```

W katalogu *SignalRWebPack* tworzona jest pusta aplikacja sieci web ASP.NET Core, kierowana na program .NET Core.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurowanie pakietu Web i kodu TypeScript

Poniższe kroki konfigurują konwersję kodu TypeScript na język JavaScript i łączenie zasobów po stronie klienta.

1. Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *package.json:*

    ```console
    npm init -y
    ```

1. Dodaj wyróżnioną właściwość do pliku *package.json:*

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Ustawienie `private` właściwości, `true` aby zapobiec ostrzeżenia instalacji pakietu w następnym kroku.

1. Zainstaluj wymagane pakiety npm. Uruchom następujące polecenie z poziomu głównego projektu:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Niektóre szczegóły polecenia, aby pamiętać:

    * Numer wersji jest `@` zgodny ze znakiem dla każdej nazwy pakietu. npm instaluje te konkretne wersje pakietów.
    * Opcja `-E` wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [przechowywania wersji semantycznych](https://semver.org/) do *pliku package.json*. Na przykład, `"webpack": "4.29.3"` jest używany `"webpack": "^4.29.3"`zamiast . Ta opcja zapobiega niezamierzonej aktualizacji do nowszych wersji pakietu.

    Aby uzyskać więcej informacji, zobacz dokumenty [npm-install.](https://docs.npmjs.com/cli/install)

1. Zastąp `scripts` właściwość pliku *package.json* następującym kodem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Niektóre wyjaśnienie skryptów:

    * `build`: Łączy zasoby po stronie klienta w trybie rozwoju i obserwuje zmiany w plikach. Obserwator plików powoduje, że pakiet do ponownego generowania za każdym razem, gdy zmienia się plik projektu. Opcja `mode` wyłącza optymalizacje produkcji, takie jak potrząsanie drzewem i minyfikacja. Używaj `build` tylko w rozwoju.
    * `release`: Łączy zasoby po stronie klienta w trybie produkcyjnym.
    * `publish`: Uruchamia `release` skrypt w celu wiązania zasobów po stronie klienta w trybie produkcyjnym. Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejsu wiersza polecenia .NET Core, aby opublikować aplikację.

1. Utwórz plik o nazwie *webpack.config.js* w katalogu głównym projektu z następującym kodem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Poprzedni plik konfiguruje kompilację webpacku. Niektóre szczegóły konfiguracji, aby pamiętać:

    * Właściwość `output` zastępuje domyślną wartość *dist*. Pakiet jest zamiast emitowane w katalogu *wwwroot.*
    * Tablica `resolve.extensions` zawiera *plik .js* do importowania javascript klienta SignalR.

1. Utwórz nowy katalog *src* w katalogu głównym projektu, aby przechowywać zasoby po stronie klienta projektu.

1. Utwórz *src/index.html* za pomocą następujących znaczników.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Poprzedni kod HTML definiuje znaczniki standardowego strony głównej.

1. Utwórz nowy katalog *src/css.* Jego celem jest przechowywanie plików *.css* projektu.

1. Utwórz *src/css/main.css* z następującymi znacznikami:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Poprzedni plik *main.css* stylizuje aplikację.

1. Utwórz *src/tsconfig.json* z następującym JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Powyższy kod konfiguruje kompilator TypeScript do tworzenia kodu JavaScript zgodnego z [kodem ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Utwórz *src/index.ts* z następującym kodem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:

    * `keyup`: To zdarzenie jest uruchamiane, gdy użytkownik wpisuje pole tekstowe. `tbMessage` Funkcja `send` jest wywoływana, gdy użytkownik naciśnie klawisz **Enter.**
    * `click`: To zdarzenie jest uruchamiane, gdy użytkownik kliknie przycisk **Wyślij.** Wywołanie funkcji `send`.

## <a name="configure-the-aspnet-core-app"></a>Konfigurowanie aplikacji ASP.NET Core

1. Kod podany w `Startup.Configure` metodzie wyświetla *Hello World!*. Zastąp wywołanie `app.Run` metody wywołaniem [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Powyższy kod umożliwia serwerowi zlokalizowanie i wyświetlenie pliku *index.html,* niezależnie od tego, czy użytkownik wprowadzi pełny adres URL, czy główny adres URL aplikacji internetowej.

1. Zadzwoń [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) w `Startup.ConfigureServices`. Dodaje usługi SignalR do projektu.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Mapuj trasę */hub* do `ChatHub` koncentratora. Dodaj następujące wiersze na `Startup.Configure`końcu:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Utwórz nowy katalog, o nazwie *Centra*, w katalogu głównym projektu. Jego celem jest przechowywanie koncentratora SignalR, który jest tworzony w następnym kroku.

1. Utwórz centrum *hubs/ChatHub.cs* z następującym kodem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Dodaj następujący kod w górnej części pliku *Startup.cs,* `ChatHub` aby rozwiązać odwołanie:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Włączanie komunikacji z klientem i serwerem

Aplikacja wyświetla obecnie prosty formularz do wysyłania wiadomości. Nic się nie dzieje, gdy próbujesz to zrobić. Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi wiadomościami.

1. Uruchom następujące polecenie w katalogu głównym projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Poprzednie polecenie instaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), co umożliwia klientowi wysyłanie wiadomości do serwera.

1. Dodaj wyróżniony kod do pliku *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Poprzedni kod obsługuje odbieranie wiadomości z serwera. Klasa `HubConnectionBuilder` tworzy nowego konstruktora do konfigurowania połączenia z serwerem. Funkcja `withUrl` konfiguruje adres URL centrum.

    SignalR umożliwia wymianę wiadomości między klientem a serwerem. Każda wiadomość ma określoną nazwę. Na przykład wiadomości o `messageReceived` nazwie można uruchomić logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie wiadomości. Nasłuchiwanie określonej wiadomości `on` można wykonać za pomocą funkcji. Możesz słuchać dowolnej liczby nazw wiadomości. Możliwe jest również przekazywanie parametrów do wiadomości, takich jak imię i nazwisko autora oraz zawartość odebranej wiadomości. Po odebraniu wiadomości przez `div` klienta tworzony jest nowy element z nazwą `innerHTML` autora i zawartością wiadomości w jego atrybucie. Nowa wiadomość jest dodawana `div` do głównego elementu wyświetlającego komunikaty.

1. Teraz, gdy klient może odbierać wiadomość, skonfiguruj ją do wysyłania wiadomości. Dodaj wyróżniony kod do pliku *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Wysyłanie wiadomości za pośrednictwem połączenia WebSockets wymaga wywołania `send` metody. Pierwszym parametrem metody jest nazwa wiadomości. Dane wiadomości zamieszkuje inne parametry. W tym przykładzie komunikat `newMessage` zidentyfikowany jako jest wysyłany do serwera. Wiadomość składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego. Jeśli wiadomość działa, wartość pola tekstowego jest wyczyszczona.

1. Dodaj metodę `NewMessage` do klasy `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Poprzedni kod emituje odebrane wiadomości do wszystkich połączonych użytkowników po serwer otrzyma je. Nie ma potrzeby, aby `on` mieć ogólną metodę, aby odbierać wszystkie wiadomości. Metoda nazwana nazwą wiadomości wystarczy.

    W tym przykładzie klient TypeScript wysyła `newMessage`wiadomość zidentyfikowaną jako . Metoda C# `NewMessage` oczekuje danych wysyłanych przez klienta. Połączenie jest nawiązywać do [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Odebrane wiadomości są wysyłane do wszystkich klientów połączonych z koncentratorem.

## <a name="test-the-app"></a>Testowanie aplikacji

Upewnij się, że aplikacja działa z następującymi krokami.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Uruchom pakiet Webpack w trybie *wydania.* Korzystając z okna **Konsola Menedżera pakietów,** uruchom następujące polecenie w katalogu głównym projektu. Jeśli nie znajdujesz się w `cd SignalRWebPack` katalogu głównym projektu, wprowadź przed wprowadzeniem polecenia.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację w przeglądarce bez dołączania debugera. Plik *wwwroot/index.html* jest obsługiwany w `http://localhost:<port_number>`pliku .

1. Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę). Wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.** Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom pakiet Webpack w trybie *wydania,* wykonując następujące polecenie w katalogu głównym projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:

    ```dotnetcli
    dotnet run
    ```

    Serwer sieci web uruchamia aplikację i udostępnia ją na localhost.

1. Otwórz przeglądarkę, aby . `http://localhost:<port_number>` Zostanie podany plik *wwwroot/index.html.* Skopiuj adres URL z paska adresu.

1. Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę). Wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.** Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
