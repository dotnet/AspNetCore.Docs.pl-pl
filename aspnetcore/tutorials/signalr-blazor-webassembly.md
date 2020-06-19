---
title: Używanie ASP.NET Core SignalR z Blazor zestawem webassembly
author: guardrex
description: Utwórz aplikację czatu, która używa ASP.NET Core SignalR z Blazor zestawem webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102697"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Używanie ASP.NET Core SignalR z Blazor zestawem webassembly

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

W tym samouczku przedstawiono podstawy tworzenia aplikacji w czasie rzeczywistym przy użyciu SignalR Blazor zestawu webassembly. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie Blazor projektu hostowanej aplikacji sieci Webassembly
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Na końcu tego samouczka będziesz mieć działającą aplikację czatu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Utwórz projekt aplikacji hostowanego elementu Blazor Webassembly

Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

> [!NOTE]
> Wymagany jest program Visual Studio 16,6 lub nowszy oraz zestaw .NET Core SDK 3.1.300 lub nowszy.

1. Tworzenie nowego projektu.

1. Wybierz pozycję ** Blazor aplikacja** i wybierz pozycję **dalej**.

1. Wpisz "BlazorSignalRApp" w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Wybierz szablon ** Blazor aplikacji webassembly** .

1. W obszarze **Zaawansowane**zaznacz pole wyboru **hostowane ASP.NET Core** .

1. Wybierz przycisk **Utwórz**.

> [!NOTE]
> Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a Blazor szablon webassembly nie jest wyświetlany w interfejsie użytkownika programu vs, należy ponownie zainstalować szablon przy użyciu `dotnet new` podanego polecenia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**. Visual Studio Code automatycznie dodaje folder *. programu vscode* z wygenerowanym *launch.jsna* plikach i *tasks.js* .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Zainstaluj najnowszą wersję programu [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/) i wykonaj następujące czynności:

1. Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.

1. Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.

1. Wybierz szablon ** Blazor aplikacji webassembly** . Wybierz przycisk **Dalej**.

   Potwierdź następujące konfiguracje:

   * **Platforma docelowa** jest ustawiona na **platformę .NET Core 3,1**.
   * **Uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.

   Zaznacz pole wyboru **hostowane ASP.NET Core** .

   Wybierz przycisk **Dalej**.

1. W polu **Nazwa projektu** Nadaj nazwę aplikacji `BlazorSignalRApp` . Wybierz przycisk **Utwórz**.

   Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj. Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.

1. Otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*. sln*).

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Dodawanie SignalR biblioteki klienta

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na *NuGet.org*.

1. Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. SignalR . Klient "w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz pozycję [Microsoft. AspNetCore. SignalR . Pakiet klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) i wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl**  >  **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że na liście rozwijanej źródła jest ustawiona wartość *NuGet.org*.

1. Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. SignalR . Klient "w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok pozycji [Microsoft. AspNetCore. SignalR . Pakiet klienta](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) i wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenia:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Dodawanie SignalR centrum

W projekcie **BlazorSignalRApp. Server** Utwórz folder *Hubs* (plural) i Dodaj następującą `ChatHub` klasę (*Hubs/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Dodaj usługi i punkt końcowy SignalR centrum

1. W projekcie **BlazorSignalRApp. Server** otwórz plik *Startup.cs* .

1. Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Dodaj SignalR i Odpowiedz usługi pośredniczące kompresji w programie `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. W pliku `Startup.Configure`:

   * Użyj oprogramowania pośredniczącego kompresji odpowiedzi w górnej części konfiguracji potoku przetwarzania.
   * Między punktami końcowymi kontrolerów i powrotu po stronie klienta należy dodać punkt końcowy centrum.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a>Dodawanie Razor kodu składnika dla rozmowy

1. W projekcie **BlazorSignalRApp. Client** Otwórz plik *Pages/index. Razor* .

1. Zastąp znacznik następującym kodem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań**wybierz projekt **BlazorSignalRApp. Server** . Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwerowej (*. programu vscode/launch.json*), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestawowi aplikacji ( `{APPLICATION NAME}.Server.dll` ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** wybierz projekt **BlazorSignalRApp. Server** . Naciśnij <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![SignalRBlazorPrzykładowa aplikacja webassembly otwarta w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie Blazor projektu hostowanej aplikacji sieci Webassembly
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Aby dowiedzieć się więcej na temat tworzenia Blazor aplikacji, zapoznaj się z Blazor dokumentacją:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:signalr/introduction>
* [SignalRnegocjowanie między źródłami na potrzeby uwierzytelniania](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
