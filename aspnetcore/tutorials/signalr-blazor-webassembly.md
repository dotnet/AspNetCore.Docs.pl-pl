---
title: Używanie ASP.NET Core SignalR Blazor z webassembly
author: guardrex
description: Utwórz aplikację do czatu, która SignalR Blazor używa ASP.NET Core z WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 03db8b48bdacec1d6877a4ea09f97c242761c42d
ms.sourcegitcommit: f976dce28ad887bbd31720c318fd4a97cf96cc6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81738010"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Użyj ASP.NET Core SignalR z Blazor WebAssembly

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Ten samouczek uczy podstaw tworzenia aplikacji w czasie rzeczywistym za pomocą SignalR z Blazor WebAssembly. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu aplikacji Hostowane w usłudze Blazor WebAssembly
> * Dodawanie biblioteki klienta SignalR
> * Dodawanie koncentratora signalr
> * Dodawanie usług SignalR i punktu końcowego dla centrum SignalR
> * Dodawanie kodu komponentu Razor do czatu

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Tworzenie hostowanego projektu aplikacji Blazor WebAssembly

Jeśli nie używasz programu Visual Studio w wersji 16.6 Preview 2 lub nowszej, zainstaluj szablon [Blazor WebAssembly.](xref:blazor/hosting-models#blazor-webassembly) Pakiet [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) ma wersję zapoznawczą, podczas gdy Blazor WebAssembly jest w wersji zapoznawczej. W powłoce polecenia wykonaj następujące polecenie:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

Postępuj zgodnie ze wskazówkami dotyczącymi wyboru narzędzi:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.

1. Wybierz **aplikację Blazor** i wybierz pozycję **Dalej**.

1. Wpisz "BlazorSignalRApp" w polu **Nazwa projektu.** Upewnij się, że wpis **Lokalizacja** jest poprawny lub podaj lokalizację dla projektu. Wybierz pozycję **Utwórz**.

1. Wybierz szablon **aplikacji Blazor WebAssembly.**

1. W **obszarze Zaawansowane**zaznacz pole wyboru **Hostowany ASP.NET Core.**

1. Wybierz pozycję **Utwórz**.

> [!NOTE]
> Jeśli uaktualniono lub zainstalowano nową wersję programu Visual Studio, a szablon Blazor WebAssembly nie pojawi się `dotnet new` w interfejsie użytkownika programu VS, zainstaluj ponownie szablon przy użyciu polecenia pokazanego wcześniej.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. W powłoce polecenia wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W programie Visual Studio Code otwórz folder projektu aplikacji.

1. Gdy pojawi się okno dialogowe, aby dodać zasoby do tworzenia i debugowania aplikacji, wybierz pozycję **Tak**. Program Visual Studio Code automatycznie dodaje folder *vscode* z wygenerowanymi plikami *launch.json* i *tasks.json.*

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. W powłoce polecenia wykonaj następujące polecenie:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. W programie Visual Studio dla komputerów Mac otwórz projekt, przechodząc do folderu projektu i otwierając plik rozwiązania projektu (*.sln*).

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

W powłoce polecenia wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Dodawanie biblioteki klienta SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp.Client** i wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że **źródło pakietu** jest ustawione na *nuget.org*.

1. Po **wybraniu opcji Przeglądaj** wpisz "Microsoft.AspNetCore.SignalR.Client" w polu wyszukiwania.

1. W wynikach wyszukiwania `Microsoft.AspNetCore.SignalR.Client` wybierz pakiet i wybierz pozycję **Zainstaluj**.

1. Jeśli zostanie wyświetlone okno dialogowe **Zmiany podglądu,** wybierz przycisk **OK**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji,** wybierz **pozycję Akceptuję,** jeśli zgadzasz się z postanowieniami licencyjnymi.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

W **terminalu zintegrowanym** **(Widok** > **terminala** z paska narzędzi) wykonaj następujące polecenia:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na **pasku** bocznym Rozwiązanie kliknij prawym przyciskiem myszy projekt **BlazorSignalRApp.Client** i wybierz polecenie **Zarządzaj pakietami NuGet**.

1. W oknie dialogowym **Zarządzanie pakietami NuGet** upewnij się, że źródło listy rozwijanej jest ustawiona *na nuget.org*.

1. Po **wybraniu opcji Przeglądaj** wpisz "Microsoft.AspNetCore.SignalR.Client" w polu wyszukiwania.

1. W wynikach wyszukiwania zaznacz pole wyboru `Microsoft.AspNetCore.SignalR.Client` obok pakietu i wybierz pozycję **Dodaj pakiet**.

1. Jeśli zostanie wyświetlone okno dialogowe **Akceptacja licencji,** wybierz pozycję **Zaakceptuj,** jeśli zgadzasz się z postanowieniami licencyjnymi.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

W powłoce poleceń wykonaj następujące polecenia:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Dodawanie koncentratora signalr

W projekcie **BlazorSignalRApp.Server** utwórz folder *Koncentratorów* (liczba `ChatHub` mnoga) i dodaj następującą klasę (*Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Dodawanie usług i punktu końcowego dla centrum SignalR

1. W projekcie **BlazorSignalRApp.Server** otwórz plik *Startup.cs.*

1. Dodaj obszar nazw `ChatHub` dla klasy do górnej części pliku:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Dodaj usługi signalr i response `Startup.ConfigureServices`compression middleware do:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Pomiędzy `Startup.Configure` punktami końcowymi dla kontrolerów i rezerwowego po stronie klienta, dodaj punkt końcowy dla koncentratora:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Dodawanie kodu komponentu Razor do czatu

1. W projekcie **BlazorSignalRApp.Client** otwórz plik *Pages/Index.razor.*

1. Zastąp znaczniki następującym kodem:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. Postępuj zgodnie ze wskazówkami dotyczącymi oprzyrządowania:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. W **Eksploratorze rozwiązań**wybierz projekt **BlazorSignalRApp.Server.** Naciśnij <kbd>klawisz F5,</kbd> aby uruchomić aplikację za pomocą debugowania lub <kbd>Ctrl</kbd>+<kbd>F5,</kbd> aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.** Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Gdy program VS Code oferuje utworzenie profilu uruchamiania aplikacji Serwer (*.vscode/launch.json),* `program` wpis jest podobny`{APPLICATION NAME}.Server.dll`do następującego, aby wskazać zestaw aplikacji ( ):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Naciśnij <kbd>klawisz F5,</kbd> aby uruchomić aplikację za pomocą debugowania lub <kbd>Ctrl</kbd>+<kbd>F5,</kbd> aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.** Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Na **pasku** bocznym rozwiązanie wybierz projekt **BlazorSignalRApp.Server.** Naciśnij przycisk -<kbd>↩</kbd><kbd>⌘</kbd>+**, <kbd>⌘</kbd>+aby uruchomić aplikację z debugowaniem lub <kbd>-</kbd>+<kbd>↩</kbd> , aby uruchomić aplikację bez debugowania.

1. Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.** Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

1. W powłoce poleceń wykonaj następujące polecenia:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Skopiuj adres URL z paska adresu, otwórz inne wystąpienie lub kartę przeglądarki i wklej adres URL na pasku adresu.

1. Wybierz jedną z tych opcji przeglądarki, wprowadź nazwę i wiadomość oraz wybierz przycisk **Wyślij.** Nazwa i wiadomość są natychmiast wyświetlane na obu stronach:

   ![SignalR Blazor WebAssembly przykładowa aplikacja otwarta w dwóch oknach przeglądarki pokazujących wymieniane wiadomości.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Cytaty: *Star Trek VI: Nieodkryty kraj* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie Blazor projektu aplikacji hostowanego przez webassembly
> * Dodawanie SignalR biblioteki klienta
> * Dodawanie SignalR koncentratora
> * Dodawanie SignalR usług i punktu SignalR końcowego dla koncentratora
> * Dodawanie kodu komponentu Razor do czatu

Aby dowiedzieć Blazor się więcej Blazor o tworzeniu aplikacji, zobacz dokumentację:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/introduction>
