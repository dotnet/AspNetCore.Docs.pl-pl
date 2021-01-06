---
title: Używanie ASP.NET Core SignalR z hostowaną Blazor WebAssembly aplikacją
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797356"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a>Używanie ASP.NET Core SignalR z hostowaną Blazor WebAssembly aplikacją

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

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* [Program Visual Studio 2019 16,8 lub nowszy](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* [Visual Studio dla komputerów Mac wersja 8,8 lub nowsza](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

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

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Wymagany jest program Visual Studio 16,8 lub nowszy oraz zestaw .NET Core SDK 5.0.0 lub nowszy.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.

::: moniker-end

1. Tworzenie nowego projektu.

1. Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.

1. Wpisz `BlazorSignalRApp` wartość w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Wybierz szablon **Blazor WebAssembly aplikacji** .

1. W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .

1. Wybierz przycisk **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**. Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .

1. Wybierz szablon **Blazor WebAssembly aplikacji** . Wybierz pozycję **Dalej**.

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**. Zaznacz pole wyboru **hostowane ASP.NET Core** . Wybierz pozycję **Dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` . Wybierz przycisk **Utwórz**.

   Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

1. Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Dodawanie SignalR biblioteki klienta

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet i wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu i wybierz polecenie **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenia:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Dodaj pakiet System. Text. encodings. Web

Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) z 5.0.0 w aplikacji ASP.NET Core 3,1, `BlazorSignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5. Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).

Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorSignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet i wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorSignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu i wybierz polecenie **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

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

::: moniker range=">= aspnetcore-5.0"

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Dodawanie Razor kodu składnika dla rozmowy

1. W `BlazorSignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.

::: moniker range=">= aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Uruchamianie aplikacji

Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań** wybierz `BlazorSignalRApp.Server` projekt. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwera ( `.vscode/launch.json` ), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestaw aplikacji ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** wybierz `BlazorSignalRApp.Server` projekt. Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor webassembly)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazująca komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

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
