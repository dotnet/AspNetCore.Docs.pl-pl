---
title: Tworzenie klienta i serwera gRPC .NET Core w ASP.NET Core
author: juntaoluo
description: W tym samouczku pokazano, jak utworzyć usługę gRPC i klienta gRPC na ASP.NET Core. Dowiedz się, jak utworzyć projekt usługi gRPC, edytować plik proto i dodać połączenie strumieniowe dupleksu.
ms.author: johluo
ms.date: 12/05/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 0cedeb021427455c3f60a8a8cc36b52794a055bc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665824"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Samouczek: Tworzenie klienta i serwera gRPC w ASP.NET Core

Przez [John Luo](https://github.com/juntaoluo)

W tym samouczku pokazano, jak utworzyć klienta [gRPC](https://grpc.io/docs/guides/) .NET Core i ASP.NET Core gRPC Server.

Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(jak pobrać).](xref:index#how-to-download-a-sample)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz serwer gRPC.
> * Utwórz klienta gRPC.
> * Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>Tworzenie usługi gRPC

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. Alternatywnie z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **gRPC Service** i wybierz pozycję **Dalej:**

  ![Tworzenie nowego okna dialogowego projektu](~/tutorials/grpc/grpc-start/static/cnp.png)

* Nazwij projekt **GrpcGreeter**. Ważne jest, aby nadać nazwę projektowi *GrpcGreeter,* aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.
* Wybierz **pozycję Utwórz**.
* W oknie **dialogowym Tworzenie nowej usługi gRPC:**
  * Zostanie wybrany szablon **usługi gRPC.**
  * Wybierz **pozycję Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.
* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Polecenie `dotnet new` tworzy nową usługę gRPC w folderze *GrpcGreeter.*
  * Polecenie `code` otwiera folder *GrpcGreeter* w nowym wystąpieniu programu Visual Studio Code.

  W "GrpcGreeter" pojawi się okno dialogowe z **wymaganymi zasobami do tworzenia i debugowania. Dodać je?**
* Wybierz **pozycję Tak**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Z terminala uruchom następujące polecenia:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Poprzednie polecenia używają [interfejsu wiersza polecenia .NET Core](/dotnet/core/tools/dotnet) do utworzenia usługi gRPC.

### <a name="open-the-project"></a>Otwieranie projektu

W programie Visual Studio wybierz pozycję**Otwieranie** **pliku** > , a następnie wybierz plik *GrpcGreeter.csproj.*

---

### <a name="run-the-service"></a>Uruchamianie usługi

  [!INCLUDE[](~/includes/run-the-app.md)]

Dzienniki pokazują, że `https://localhost:5001`usługa nasłuchuje .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Szablon gRPC jest skonfigurowany do [używania zabezpieczeń warstwy transportowej (TLS).](https://tools.ietf.org/html/rfc5246) Klienci gRPC muszą używać protokołu HTTPS do wywoływania serwera.
>
> system macOS nie obsługuje ASP.NET core gRPC z TLS. Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS. Aby uzyskać więcej informacji, zobacz [Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Pliki projektu *GrpcGreeter:*

* *greet.proto* &ndash; Plik *Protos/greet.proto* definiuje `Greeter` gRPC i jest używany do generowania aktywów serwera gRPC. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do gRPC](xref:grpc/index).
* *Folder usług:* Zawiera implementację `Greeter` usługi.
* *appSettings.json* &ndash; Zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.
* *Program.cs* &ndash; Zawiera punkt wejścia dla usługi gRPC. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.
* *Startup.cs* &ndash; Zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Tworzenie klienta gRPC w aplikacji konsoli .NET

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz drugie wystąpienie programu Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
* W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **Aplikacja konsoli (.NET Core)** i wybierz pozycję **Dalej**.
* W polu tekstowym **Nazwa** wprowadź **grpcGreeterClient** i wybierz pozycję **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.
* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Postępuj zgodnie z instrukcjami w [tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac,](/dotnet/core/tutorials/using-on-mac-vs-full-solution) aby utworzyć aplikację konsoli o nazwie *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Dodawanie wymaganych pakietów

Projekt klienta gRPC wymaga następujących pakietów:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API wiadomości protobuf dla języka C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), który zawiera c# obsługa narzędzi dla plików protobuf. Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest oznaczona `PrivateAssets="All"`.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub zarządzania pakietami NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opcja instalacji pakietów PMC

* W programie Visual Studio wybierz pozycję **Konsola** > Menedżera > **pakietów** **programu Narzędzia NuGet Package**Manager
* W oknie **Konsola Menedżera pakietów** uruchom, `cd GrpcGreeterClient` aby zmienić katalogi do folderu zawierającego pliki *GrpcGreeterClient.csproj.*
* Uruchom następujące polecenia:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Zarządzanie pakietami NuGet, aby zainstalować pakiety

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**
* Wybierz kartę **Przeglądaj**.
* Wprowadź **grpc.Net.Client** w polu wyszukiwania.
* Wybierz pakiet **Grpc.Net.Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.
* Powtarzanie `Google.Protobuf` `Grpc.Tools`dla i .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenia z **terminala zintegrowanego:**

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder **Pakiety** w **panelu rozrachowy** > **Dodawanie pakietów**
* Wprowadź **grpc.Net.Client** w polu wyszukiwania.
* Wybierz pakiet **Grpc.Net.Client** z okienka wyników i wybierz **dodaj pakiet**
* Powtarzanie `Google.Protobuf` `Grpc.Tools`dla i .

---

### <a name="add-greetproto"></a>Dodaj greet.proto

* Utwórz folder *Protos* w projekcie klienta gRPC.
* Skopiuj plik *Protos\greet.proto* z usługi gRPC Greeter do projektu klienta gRPC.
* Edytuj plik projektu *GrpcGreeterClient.csproj:*

  # <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

  Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Wybierz plik *GrpcGreeterClient.csproj.*

  # <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Narzędzia** > **edytuj plik**.

  ---

* Dodaj grupę elementów z elementem, `<Protobuf>` który odwołuje się do pliku *greet.proto:*

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Tworzenie klienta greeter

Skompiluj projekt, `GrpcGreeter` aby utworzyć typy w obszarze nazw. Typy `GrpcGreeter` są generowane automatycznie przez proces kompilacji.

Zaktualizuj plik *Program.cs* klienta gRPC za pomocą następującego kodu:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.

Klient Greeter jest tworzony przez:

* Tworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.
* Za `GrpcChannel` pomocą do konstruowania klienta Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Klient Greeter wywołuje metodę asynchroniczne. `SayHello` Wynik `SayHello` połączenia jest wyświetlany:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Przetestuj klienta gRPC za pomocą usługi gRPC Greeter

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W usłudze Greeter `Ctrl+F5` naciśnij, aby uruchomić serwer bez debugera.
* W `GrpcGreeterClient` projekcie naciśnij, `Ctrl+F5` aby uruchomić klienta bez debugera.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom usługę Greeter.
* Uruchom klienta.


# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Uruchom usługę Greeter.
* Uruchom klienta.

---

Klient wysyła powitanie do usługi z komunikatem zawierającym jego nazwę *GreeterClient*. Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź. Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach zapisanych w wierszu polecenia:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Kod w tym artykule wymaga ASP.NET certyfikatu rozwoju HTTPS Core w celu zabezpieczenia usługi gRPC. Jeśli klient nie powiedzie się z komunikatem, `The remote certificate is invalid according to the validation procedure.`certyfikat dewelopera nie jest zaufany. Aby uzyskać instrukcje dotyczące rozwiązania tego problemu, zobacz [Ufanie ASP.NET podstawowy certyfikat rozwoju HTTPS w systemach Windows i macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Następne kroki

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
