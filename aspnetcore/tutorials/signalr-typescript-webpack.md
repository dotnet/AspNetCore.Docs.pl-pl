---
title: Używanie ASP.NET Core SignalR z językami TypeScript i WebPack
author: ssougnez
description: W tym samouczku skonfigurujesz pakiet WebPack do tworzenia pakietów i kompilowania ASP.NET Core SignalR aplikacji sieci Web, której klient został zapisany w języku TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056845"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a>Używanie ASP.NET Core SignalR z językami TypeScript i WebPack

Autorzy [Sébastien Sougnez](https://twitter.com/ssougnez) i [Scott Addie](https://twitter.com/Scott_Addie)

[Pakiet WebPack](https://webpack.js.org/) umożliwia deweloperom tworzenie i kompilowanie zasobów po stronie klienta aplikacji sieci Web. W tym samouczku pokazano, jak używać pakietu WebPack w ASP.NET Core SignalR aplikacji sieci Web, której klient został zapisany w języku [TypeScript](https://www.typescriptlang.org/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szkieletu aplikacji Starter ASP.NET Core SignalR
> * Konfigurowanie SignalR klienta TypeScript
> * Konfigurowanie potoku kompilacji przy użyciu pakietu WebPack
> * Konfigurowanie SignalR serwera
> * Włącz komunikację między klientem a serwerem

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Zestaw .NET Core SDK 3.0 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [C# dla Visual Studio Code w wersji 1.17.1 lub nowszej](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Tworzenie aplikacji sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* . Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym. Wykonaj te instrukcje w programie Visual Studio:

1. Uruchom program Visual Studio. W oknie uruchamiania wybierz pozycję **Kontynuuj bez kodu** .
1. Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web** .
1. Wybierz z listy wpis *$ (Path)* . Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście, a następnie wybierz **przycisk OK** .

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfiguracja programu Visual Studio została ukończona.

1. Użyj opcji menu **plik**  >  **Nowy**  >  **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** . Wybierz opcję **Dalej** .
1. Nadaj nazwę projekt *SignalR WebPack* , a następnie wybierz pozycję **Utwórz** .
1. Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 3,1* z listy rozwijanej selektora struktury. Wybierz **pusty** szablon i wybierz pozycję **Utwórz** .

Dodaj `Microsoft.TypeScript.MSBuild` pakiet do projektu:

1. W **Eksplorator rozwiązań** (prawego okienka) kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Zarządzaj pakietami NuGet** . Na karcie **Przeglądaj** Wyszukaj `Microsoft.TypeScript.MSBuild` pozycję, a następnie kliknij pozycję **Zainstaluj** po prawej stronie, aby zainstalować pakiet.

Program Visual Studio dodaje pakiet NuGet w węźle **zależności** w **Eksplorator rozwiązań** , włączając kompilację języka TypeScript w projekcie.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **zintegrowanym terminalu** :

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* `dotnet new`Polecenie tworzy pustą aplikację sieci web ASP.NET Core w katalogu *SignalR pakietu WebPack* .
* `code`Polecenie otwiera folder *SignalR WebPack* w bieżącym wystąpieniu Visual Studio Code.

Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core w **zintegrowanym terminalu** :

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Poprzednie polecenie dodaje pakiet [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , włączając kompilację TypeScript w projekcie.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurowanie pakietu WebPack i języka TypeScript

Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.

1. Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć *package.jsw* pliku:

    ```console
    npm init -y
    ```

1. Dodaj wyróżnioną właściwość do *package.jsw* pliku i Zapisz zmiany w pliku:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.

1. Zainstaluj wymagane pakiety npm. Uruchom następujące polecenie z poziomu głównego projektu:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Niektóre szczegóły polecenia do uwagi:

    * Numer wersji następuje po `@` znaku dla każdej nazwy pakietu. npm instaluje te określone wersje pakietu.
    * `-E`Opcja wyłącza domyślne zachowanie npm w pisaniu operatorów zakresu [wersji semantycznej](https://semver.org/) w celu *package.jsna* . Na przykład, `"webpack": "4.41.5"` jest używany zamiast `"webpack": "^4.41.5"` . Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.

    Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .

1. Zastąp `scripts` właściwość *package.jsw* pliku następującym kodem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Niektóre objaśnienia dotyczące skryptów:

    * `build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku. Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany. `mode`Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa. Używać tylko `build` w programowaniu.
    * `release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.
    * `publish`: Uruchamia skrypt służący `release` do łączenia zasobów po stronie klienta w trybie produkcyjnym. Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.

1. Utwórz plik o nazwie *webpack.config.js* , w katalogu głównym projektu, przy użyciu następującego kodu:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Poprzedni plik konfiguruje kompilację pakietu WebPack. Niektóre szczegóły konfiguracji do uwagi:

    * `output`Właściwość zastępuje domyślną wartość *rozkłu* . Pakiet jest emitowany w katalogu *wwwroot* .
    * `resolve.extensions`Tablica zawiera kod *js* do zaimportowania SignalR klienta JavaScript.

1. Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.

1. Utwórz *src/index.html* z następującą adiustacją.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Powyższy kod HTML definiuje standardowe znaczniki strony głównej.

1. Utwórz nowy katalog *src/CSS* . Celem jest przechowywanie plików *CSS* projektu.

1. Utwórz *src/CSS/Main. css* z następującym arkuszem CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Poprzedni plik *Main. css* jest stylem aplikacji.

1. Utwórz *src/tsconfig.js* przy użyciu następującego kodu JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Utwórz *src/index. TS* przy użyciu następującego kodu:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:

    * `keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze wartość w polu `tbMessage` tekstowym. `send`Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .
    * `click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** . Wywołanie funkcji `send`.

## <a name="configure-the-app"></a>Konfigurowanie aplikacji

1. W programie `Startup.Configure` Dodaj wywołania do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index.html* .  Plik jest obsługiwany niezależnie od tego, czy użytkownik wprowadza pełny adres URL, czy też główny adres URL aplikacji sieci Web.

1. Na końcu `Startup.Configure` należy zmapować */Hub* trasę do `ChatHub` centrum. Zastąp kod, który wyświetla *Hello World!* z następującym wierszem: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. W programie `Startup.ConfigureServices` Wywołaj polecenie [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Utwórz nowy katalog o nazwie *Hubs* w głównym programie Project *SignalR /* w celu zapisania SignalR centrum.

1. Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Dodaj następującą `using` instrukcję w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Włączanie komunikacji klienta i serwera

W aplikacji jest obecnie wyświetlany podstawowy formularz służący do wysyłania komunikatów, ale nie jest on jeszcze funkcjonalny. Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.

1. Uruchom następujące polecenie w katalogu głównym projektu:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Poprzednie polecenie instaluje:

     * [ SignalR Klient języka TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.
     * Definicje typu TypeScript dla Node.js, które umożliwiają sprawdzanie w czasie kompilacji typów Node.js.

1. Dodaj wyróżniony kod do pliku *src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Poprzedni kod obsługuje otrzymywanie komunikatów z serwera. `HubConnectionBuilder`Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem. `withUrl`Funkcja konfiguruje adres URL centrum.

    SignalR Włącza wymianę komunikatów między klientem a serwerem. Każdy komunikat ma określoną nazwę. Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages. Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji. Dowolna liczba nazw komunikatów może być nasłuchiwanie. Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości. Po odebraniu komunikatu przez klienta `div` zostanie utworzony nowy element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie. Jest on dodawany do elementu głównego `div` wyświetlającego komunikaty.

1. Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości. Dodaj wyróżniony kod do pliku *src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody. Pierwszym parametrem metody jest nazwa komunikatu. Dane komunikatu są nieodpowiednie dla innych parametrów. W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera. Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego. Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.

1. Dodaj metodę `NewMessage` do klasy `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer. Nie jest konieczne posiadanie `on` metody ogólnej do odbierania wszystkich komunikatów. Metoda o nazwie po wystarczającej nazwie.

    W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage` . Metoda języka C# `NewMessage` oczekuje danych wysyłanych przez klienta. Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.

## <a name="test-the-app"></a>Testowanie aplikacji

Upewnij się, że aplikacja działa z następującymi krokami.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Uruchom pakiet WebPack w trybie *wydania* . Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu. Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd SignalRWebPack` przed wprowadzeniem polecenia.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera. Plik *wwwroot/index.html* jest obsługiwany pod adresem `http://localhost:<port_number>` .

   Jeśli zostaną wyświetlone błędy kompilacji, spróbuj zamknąć i ponownie otworzyć rozwiązanie. 

1. Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka). Wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** . Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:

    ```dotnetcli
    dotnet run
    ```

    Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.

1. Otwórz przeglądarkę do programu `http://localhost:<port_number>` . Plik *wwwroot/index.html* jest obsługiwany. Skopiuj adres URL z paska adresu.

1. Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka). Wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** . Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [Zestaw .NET Core SDK 2,2 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Zestaw .NET Core SDK 2,2 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [C# dla Visual Studio Code w wersji 1.17.1 lub nowszej](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Tworzenie aplikacji sieci Web ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* . Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym. Wykonaj te instrukcje w programie Visual Studio:

1. Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web** .
1. Wybierz z listy wpis *$ (Path)* . Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście.

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfiguracja programu Visual Studio została ukończona. Czas na utworzenie projektu.

1. Użyj opcji menu **plik** > **Nowy** > **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** .
1. Nadaj nazwę projekt *SignalR WebPack* , a następnie wybierz pozycję **Utwórz** .
1. Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 2,2* z listy rozwijanej selektora struktury. Wybierz **pusty** szablon i wybierz pozycję **Utwórz** .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **zintegrowanym terminalu** :

```dotnetcli
dotnet new web -o SignalRWebPack
```

Pusta aplikacja sieci Web ASP.NET Core, ukierunkowana na .NET Core, jest tworzona w katalogu *SignalR pakietu WebPack* .

---

## <a name="configure-webpack-and-typescript"></a>Konfigurowanie pakietu WebPack i języka TypeScript

Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.

1. Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć *package.jsw* pliku:

    ```console
    npm init -y
    ```

1. Dodaj wyróżnioną właściwość do *package.jsw* pliku:

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.

1. Zainstaluj wymagane pakiety npm. Uruchom następujące polecenie z poziomu głównego projektu:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Niektóre szczegóły polecenia do uwagi:

    * Numer wersji następuje po `@` znaku dla każdej nazwy pakietu. npm instaluje te określone wersje pakietu.
    * `-E`Opcja wyłącza domyślne zachowanie npm w pisaniu operatorów zakresu [wersji semantycznej](https://semver.org/) w celu *package.jsna* . Na przykład, `"webpack": "4.29.3"` jest używany zamiast `"webpack": "^4.29.3"` . Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.

    Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .

1. Zastąp `scripts` właściwość *package.jsw* pliku następującym kodem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Niektóre objaśnienia dotyczące skryptów:

    * `build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku. Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany. `mode`Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa. Używać tylko `build` w programowaniu.
    * `release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.
    * `publish`: Uruchamia skrypt służący `release` do łączenia zasobów po stronie klienta w trybie produkcyjnym. Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.

1. Utwórz plik o nazwie *webpack.config.js* w katalogu głównym projektu przy użyciu następującego kodu:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Poprzedni plik konfiguruje kompilację pakietu WebPack. Niektóre szczegóły konfiguracji do uwagi:

    * `output`Właściwość zastępuje domyślną wartość *rozkłu* . Pakiet jest emitowany w katalogu *wwwroot* .
    * `resolve.extensions`Tablica zawiera kod *js* do zaimportowania SignalR klienta JavaScript.

1. Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.

1. Utwórz *src/index.html* z następującą adiustacją.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Powyższy kod HTML definiuje standardowe znaczniki strony głównej.

1. Utwórz nowy katalog *src/CSS* . Celem jest przechowywanie plików *CSS* projektu.

1. Utwórz *src/CSS/Main. css* z następującą adiustacją:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Poprzedni plik *Main. css* jest stylem aplikacji.

1. Utwórz *src/tsconfig.js* przy użyciu następującego kodu JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Utwórz *src/index. TS* przy użyciu następującego kodu:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:

    * `keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze wartość w polu `tbMessage` tekstowym. `send`Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .
    * `click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** . Wywołanie funkcji `send`.

## <a name="configure-the-aspnet-core-app"></a>Konfigurowanie aplikacji ASP.NET Core

1. Kod podany w `Startup.Configure` metodzie wyświetla *Hello World!* . Zastąp `app.Run` wywołanie metody wywołaniami do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index.html* , niezależnie od tego, czy użytkownik wprowadzi pełny adres URL, czy główny adres URL aplikacji sieci Web.

1. Wywołanie [Add SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices` . Dodaje SignalR usługi do projektu.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Mapuj trasę */Hub* do `ChatHub` centrum. Dodaj następujące wiersze na końcu `Startup.Configure` :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Utwórz nowy katalog o nazwie *Hubs* w katalogu głównym projektu. Celem jest przechowywanie SignalR centrum, które jest tworzone w następnym kroku.

1. Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Dodaj następujący kod w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Włączanie komunikacji klienta i serwera

W aplikacji jest obecnie wyświetlany prosty formularz służący do wysyłania komunikatów. Nic się nie dzieje, gdy użytkownik spróbuje to zrobić. Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.

1. Uruchom następujące polecenie w katalogu głównym projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Poprzednie polecenie instaluje klienta języka [ SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.

1. Dodaj wyróżniony kod do pliku *src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Poprzedni kod obsługuje otrzymywanie komunikatów z serwera. `HubConnectionBuilder`Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem. `withUrl`Funkcja konfiguruje adres URL centrum.

    SignalR Włącza wymianę komunikatów między klientem a serwerem. Każdy komunikat ma określoną nazwę. Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages. Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji. Można nasłuchiwać dowolnej liczby nazw komunikatów. Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości. Po odebraniu komunikatu przez klienta `div` zostanie utworzony nowy element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie. Nowa wiadomość zostanie dodana do głównego `div` elementu wyświetlającego komunikaty.

1. Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości. Dodaj wyróżniony kod do pliku *src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody. Pierwszym parametrem metody jest nazwa komunikatu. Dane komunikatu są nieodpowiednie dla innych parametrów. W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera. Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego. Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.

1. Dodaj metodę `NewMessage` do klasy `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer. Nie jest konieczne posiadanie `on` metody ogólnej do odbierania wszystkich komunikatów. Metoda o nazwie po wystarczającej nazwie.

    W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage` . Metoda języka C# `NewMessage` oczekuje danych wysyłanych przez klienta. Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.

## <a name="test-the-app"></a>Testowanie aplikacji

Upewnij się, że aplikacja działa z następującymi krokami.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Uruchom pakiet WebPack w trybie *wydania* . Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu. Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd SignalRWebPack` przed wprowadzeniem polecenia.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera. Plik *wwwroot/index.html* jest obsługiwany pod adresem `http://localhost:<port_number>` .

1. Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka). Wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** . Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:

    ```dotnetcli
    dotnet run
    ```

    Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.

1. Otwórz przeglądarkę do programu `http://localhost:<port_number>` . Plik *wwwroot/index.html* jest obsługiwany. Skopiuj adres URL z paska adresu.

1. Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka). Wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** . Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
