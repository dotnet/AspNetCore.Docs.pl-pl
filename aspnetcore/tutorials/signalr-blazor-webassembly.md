---
title: Używanie ASP.NET Core SignalR z Blazor zestawem webassembly
author: guardrex
description: Utwórz aplikację czatu, która używa ASP.NET Core SignalR z Blazor zestawem webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773791"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Korzystanie z ASP.NET Core sygnalizującego z zestawem webassembly Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

W tym samouczku przedstawiono podstawowe informacje na temat tworzenia aplikacji w czasie rzeczywistym przy użyciu usługi sygnalizującej z zestawem webBlazor. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu hostowanej aplikacji sieci webassembly Blazor
> * Dodawanie biblioteki klienta sygnalizującego
> * Dodawanie centrum sygnałów
> * Dodaj usługi sygnalizujące i punkt końcowy centrum sygnałów
> * Dodawanie kodu składnika Razor dla rozmowy

Na końcu tego samouczka będziesz mieć działającą aplikację czatu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Utwórz projekt aplikacji hostowanej Blazor webassembly

Gdy nie korzystasz z programu Visual Studio w wersji 16,6 Preview 2 lub nowszej, zainstaluj szablon [Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) . Pakiet [Microsoft. AspNetCore. Components. webassembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ma wersję zapoznawczą, a Blazor webassembly jest w wersji zapoznawczej. W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.

1. Wybierz pozycję **aplikacja Blazor** i wybierz pozycję **dalej**.

1. Wpisz "BlazorSignalRApp" w polu **Nazwa projektu** . Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu. Wybierz przycisk **Utwórz**.

1. Wybierz szablon **aplikacji Webassembly Blazor** .

1. W obszarze **Zaawansowane**zaznacz pole wyboru **hostowane ASP.NET Core** .

1. Wybierz przycisk **Utwórz**.

> [!NOTE]
> Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a szablon Blazor webassembly nie jest wyświetlany w interfejsie użytkownika programu VS, należy `dotnet new` ponownie zainstalować szablon przy użyciu podanego wcześniej polecenia.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W Visual Studio Code Otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe dodawania zasobów do kompilowania i debugowania aplikacji, wybierz pozycję **tak**. Visual Studio Code automatycznie dodaje folder *. programu vscode* z wygenerowanymi plikami *Launch. JSON* i *Tasks. JSON* .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. W powłoce poleceń wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W Visual Studio dla komputerów Mac otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*. sln*).

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta sygnalizującego

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **Źródło pakietów** jest ustawione na *NuGet.org*.

1. Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. signaler. Client" w polu wyszukiwania.

1. W wynikach wyszukiwania wybierz `Microsoft.AspNetCore.SignalR.Client` pakiet i wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Podgląd zmian** , wybierz **przycisk OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuję** , jeśli akceptujesz postanowienia licencyjne.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **zintegrowanym terminalu** (**Wyświetl** > **Terminal** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp. Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że na liście rozwijanej źródła jest ustawiona wartość *NuGet.org*.

1. Po wybraniu **przycisku Przeglądaj** wpisz "Microsoft. AspNetCore. signaler. Client" w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru obok `Microsoft.AspNetCore.SignalR.Client` pakietu i wybierz polecenie **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji** , wybierz pozycję **Akceptuj** , jeśli akceptujesz postanowienia licencyjne.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenia:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Dodawanie centrum sygnałów

W projekcie **BlazorSignalRApp. Server** Utwórz folder *Hubs* (plural) i Dodaj następującą `ChatHub` klasę (*Hubs/ChatHub. cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Dodaj usługi i punkt końcowy centrum sygnałów

1. W projekcie **BlazorSignalRApp. Server** otwórz plik *Startup.cs* .

1. Dodaj przestrzeń nazw dla `ChatHub` klasy na początku pliku:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Dodaj sygnały sygnalizujące i odpowiedzi na kompresję usług pośredniczących do `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. W `Startup.Configure` obszarze punkty końcowe dla kontrolerów i powrotu po stronie klienta Dodaj punkt końcowy centrum:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Dodawanie kodu składnika Razor dla rozmowy

1. W projekcie **BlazorSignalRApp. Client** Otwórz plik *Pages/index. Razor* .

1. Zastąp znacznik następującym kodem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Postępuj zgodnie ze wskazówkami dotyczącymi narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksplorator rozwiązań**wybierz projekt **BlazorSignalRApp. Server** . Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Gdy VS Code oferuje możliwość utworzenia profilu uruchamiania dla aplikacji serwerowej (*. programu vscode/Launch. JSON*), `program` wpis będzie wyglądać podobnie do poniższego, aby wskazać zestawowi aplikacji (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Naciśnij klawisz <kbd>F5</kbd> , aby uruchomić aplikację z debugowaniem lub <kbd>klawiszem Ctrl</kbd>+<kbd>F5</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na pasku bocznym **rozwiązania** wybierz projekt **BlazorSignalRApp. Server** . Naciśnij <kbd>⌘</kbd>+<kbd>↩</kbd>* *, aby uruchomić aplikację z debugowaniem lub <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, Otwórz inne wystąpienie przeglądarki lub kartę, a następnie wklej adres URL na pasku adresu.

1. Wybierz opcję przeglądarka, wprowadź nazwę i komunikat, a następnie wybierz przycisk **Wyślij** . Nazwa i komunikat są wyświetlane na obu stronach natychmiast:

   ![Przykładowa aplikacja usługi Blazor webassembly otwiera się w dwóch oknach przeglądarki, w których wyświetlane są komunikaty wymieniane.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cudzysłowy: *gwiazdka Trek VI: niewykrywalny kraj* &copy; [1991](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie projektu Blazor hostowanej aplikacji sieci webassembly
> * Dodawanie biblioteki SignalR klienta
> * Dodawanie SignalR centrum
> * Dodaj SignalR usługi i punkt końcowy SignalR centrum
> * Dodawanie Razor kodu składnika dla rozmowy

Aby dowiedzieć się więcej Blazor na temat tworzenia aplikacji Blazor , zapoznaj się z dokumentacją:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:signalr/introduction>
