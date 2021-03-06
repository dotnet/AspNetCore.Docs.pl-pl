---
title: Użyj ASP.NET Core SignalR z Blazor
author: guardrex
description: Utwórz aplikację czatu korzystającą z ASP.NET Core SignalR z Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 50e5f2d5b1f9d0bf229bc57e6a1f599f9574b27c
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395230"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>Użyj ASP.NET Core SignalR z Blazor

Ten samouczek uczy się podstaw tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR programu z usługą Blazor . Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie Blazor projektu
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Na końcu tego samouczka będziesz mieć działającą aplikację czatu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

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

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>Tworzenie aplikacji hostowanej Blazor WebAssembly

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

1. Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.

1. Wpisz `BlazorWebAssemblySignalRApp` wartość w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Wybierz szablon **Blazor WebAssembly aplikacji** .

1. W obszarze **Zaawansowane** zaznacz pole wyboru **hostowane ASP.NET Core** .

1. Wybierz przycisk **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   `-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly . Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .

   `-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania. Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**. Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .

1. Wybierz szablon **Blazor WebAssembly aplikacji** . Wybierz opcję **Dalej**.

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**. Zaznacz pole wyboru **hostowane ASP.NET Core** . Wybierz opcję **Dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorWebAssemblySignalRApp` . Wybierz przycisk **Utwórz**.

   Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

1. Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

`-ho|--hosted`Opcja tworzy rozwiązanie hostowane Blazor WebAssembly .

`-o|--output`Opcja powoduje utworzenie folderu dla rozwiązania. Jeśli utworzono folder dla rozwiązania, a powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć rozwiązanie.

---

## <a name="add-the-signalr-client-library"></a>Dodawanie SignalR biblioteki klienta

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet. Ustaw wersję na zgodną z udostępnioną strukturą aplikacji. Wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Client` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu. Ustaw wersję na zgodną z udostępnioną strukturą aplikacji. Wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Dodaj pakiet System. Text. encodings. Web

*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*

Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x `BlazorWebAssemblySignalRApp.Server` projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5. Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).

Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do `BlazorWebAssemblySignalRApp.Server` projektu rozwiązania hostowanego ASP.NET Core 3,1 Blazor , postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet. Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu. Wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorWebAssemblySignalRApp.Server` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenie:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Dodawanie SignalR centrum

W `BlazorWebAssemblySignalRApp.Server` projekcie Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Dodaj usługi i punkt końcowy SignalR centrum

1. W `BlazorWebAssemblySignalRApp.Server` projekcie Otwórz `Startup.cs` plik.

1. Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Dodawanie Razor kodu składnika dla rozmowy

1. W `BlazorWebAssemblySignalRApp.Client` projekcie Otwórz `Pages/Index.razor` plik.

::: moniker range=">= aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Uruchamianie aplikacji

Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań** wybierz `BlazorWebAssemblySignalRApp.Server` projekt. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** wybierz `BlazorWebAssemblySignalRApp.Server` projekt. Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń z folderu rozwiązania wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>Tworzenie Blazor Server aplikacji

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

1. Wybierz pozycję **Blazor aplikacja** i wybierz pozycję **dalej**.

1. Wpisz `BlazorServerSignalRApp` wartość w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Wybierz szablon **Blazor Server aplikacji** .

1. Wybierz przycisk **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   `-o|--output`Opcja powoduje utworzenie folderu dla projektu. Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**. Visual Studio Code automatycznie dodaje `.vscode` folder z wygenerowanymi `launch.json` `tasks.json` plikami i. Aby uzyskać informacje dotyczące konfigurowania VS Code zasobów w `.vscode` folderze, zobacz Wskazówki dotyczące systemu operacyjnego **Linux** w temacie <xref:blazor/tooling> .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .

1. Wybierz szablon **Blazor Server aplikacji** . Wybierz opcję **Dalej**.

1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**. Wybierz opcję **Dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorServerSignalRApp` . Wybierz przycisk **Utwórz**.

   Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

1. Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu ( `.sln` ).

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

`-o|--output`Opcja powoduje utworzenie folderu dla projektu. Jeśli utworzono folder dla projektu i powłoka poleceń jest otwarta w tym folderze, Pomiń `-o|--output` opcję i wartość, aby utworzyć projekt.

---

## <a name="add-the-signalr-client-library"></a>Dodawanie SignalR biblioteki klienta

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakiet. Ustaw wersję na zgodną z udostępnioną strukturą aplikacji. Wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) Uruchom następujące polecenie:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `Microsoft.AspNetCore.SignalR.Client` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) pakietu. Ustaw wersję na zgodną z udostępnioną strukturą aplikacji. Wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń z folderu projektu wykonaj następujące polecenie:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Dodaj pakiet System. Text. encodings. Web

*Ta sekcja dotyczy tylko aplikacji dla ASP.NET Core w wersji 3. x.*

Ze względu na problem z rozpoznawaniem pakietu w przypadku korzystania z programu [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5. x w aplikacji ASP.NET Core 3. x projekt wymaga odwołania do pakietu [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) . Podstawowy problem zostanie rozwiązany w przyszłej wersji poprawki programu .NET 5. Aby uzyskać więcej informacji, zobacz [System.Text.Json definiuje netcoreapp 3.0 bez zależności (#45560 dotnet/Runtime)](https://github.com/dotnet/runtime/issues/45560).

Aby dodać [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) do projektu, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakiet. Wybierz wersję pakietu pasującą do udostępnionej platformy w użyciu. Wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy `BlazorServerSignalRApp` projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że lista rozwijana źródłowa ma ustawioną wartość `nuget.org` .

1. Po wybraniu **przycisku Przeglądaj** wpisz `System.Text.Encodings.Web` w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) pakietu, wybierz odpowiednią wersję pakietu odpowiadającą udostępnionej platformie, a następnie wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń z folderu projektu wykonaj następujące polecenie:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Aby dodać wcześniejszą wersję pakietu, należy podać `--version {VERSION}` opcję, gdzie `{VERSION}` symbol zastępczy jest wersją pakietu do dodania.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Dodawanie SignalR centrum

Utwórz `Hubs` folder (plural) i Dodaj następującą `ChatHub` klasę ( `Hubs/ChatHub.cs` ):

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Dodaj usługi i punkt końcowy SignalR centrum

1. Otwórz plik `Startup.cs`.

1. Dodaj przestrzenie nazw dla <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> i `ChatHub` klasy na początku pliku:

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Dodaj usługi oprogramowania pośredniczącego kompresji odpowiedzi do programu `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi mapowania Blazor centrum i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Dodawanie Razor kodu składnika dla rozmowy

1. Otwórz plik `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Zastąp znacznik następującym kodem:

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Uruchamianie aplikacji

Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń z folderu projektu wykonaj następujące polecenia:

   ```dotnetcli
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk, aby wysłać wiadomość. Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![::: No-Loc (Sygnalizującer):::.:: No-Loc (Blazor)::: przykładowa aplikacja otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor/_static/signalr-blazor-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie Blazor projektu
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
* [SignalR negocjowanie między źródłami na potrzeby uwierzytelniania](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
