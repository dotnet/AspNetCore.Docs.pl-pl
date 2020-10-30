---
title: Użyj ASP.NET Core SignalR z Blazor WebAssembly
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052711"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Użyj ASP.NET Core SignalR z Blazor WebAssembly

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor WebAssembly . Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie Blazor WebAssembly projektu hostowanej aplikacji
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Na końcu tego samouczka będziesz mieć działającą aplikację czatu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* [Program Visual Studio 2019 16,8 lub nowszy (w wersji zapoznawczej)](https://visualstudio.microsoft.com/vs/preview/) z **ASP.NET i programowaniem aplikacji sieci Web**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [Visual Studio dla komputerów Mac wersja 8,8 lub nowsza (w wersji zapoznawczej)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Program Visual Studio 2019 16,6 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [Visual Studio dla komputerów Mac wersja 8,6 lub nowsza](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Tworzenie projektu hostowanej Blazor WebAssembly aplikacji

Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.

::: moniker-end

1. Tworzenie nowego projektu.

1. Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej** .

1. Wpisz `BlazorSignalRApp` wartość w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz pozycję **Utwórz** .

1. Wybierz szablon **Blazor WebAssembly aplikacji** .

1. W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .

1. Wybierz pozycję **Utwórz** .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak** . Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania** .

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App** .

1. Wybierz szablon **Blazor WebAssembly aplikacji** . Wybierz opcję **Dalej** .

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** . Zaznacz pole wyboru **hostowane ASP.NET Core** . Wybierz opcję **Dalej** .

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` . Wybierz pozycję **Utwórz** .

   Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

1. Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Dodawanie SignalR biblioteki klienta

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet** .

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet i wybierz pozycję **Zainstaluj** .

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK** .

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** ( **Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet** .

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu i wybierz polecenie **Dodaj pakiet** .

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenia:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Dodawanie SignalR centrum

W `BlazorSignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Dodaj usługi i punkt końcowy SignalR centrum

1. W `BlazorSignalRApp.Server` projekcie Otwórz `Startup.cs` plik.

1. Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Dodawanie Razor kodu składnika dla rozmowy

1. W `BlazorSignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.

1. Zastąp znacznik następującym kodem:

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Uruchamianie aplikacji

1. Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań** wybierz `BlazorSignalRApp.Server` projekt. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** wybierz `BlazorSignalRApp.Server` projekt. Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie Blazor WebAssembly projektu hostowanej aplikacji
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Uwierzytelnianie tokenu okaziciela z użyciem Identity serwera, obiektów WebSockets i zdarzeń Server-Sent](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/introduction>
* [SignalR negocjowanie między źródłami na potrzeby uwierzytelniania](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
