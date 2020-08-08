---
title: Tworzenie klienta i serwera platformy .NET Core gRPC w ASP.NET Core
author: juntaoluo
description: W tym samouczku pokazano, jak utworzyć usługę gRPC i klienta gRPC na ASP.NET Core. Dowiedz się, jak utworzyć projekt usługi gRPC, edytować plik PROTO i dodać wywołanie przesyłania strumieniowego dupleksu.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: b3c210e50f4bf2869100976176ded939b39e3712
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022059"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Samouczek: Tworzenie gRPC klienta i serwera w ASP.NET Core

Przez [Jan Luo](https://github.com/juntaoluo)

W tym samouczku pokazano, jak utworzyć klienta programu .NET Core [gRPC](https://grpc.io/docs/guides/) oraz serwer gRPC ASP.NET Core.

Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz serwer gRPC.
> * Utwórz klienta gRPC.
> * Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>Tworzenie usługi gRPC

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. Alternatywnie z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt**.
* W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **Usługa gRPC** i wybierz pozycję **dalej**:

  ![Okno dialogowe Tworzenie nowego projektu](~/tutorials/grpc/grpc-start/static/cnp.png)

* Nazwij projekt **GrpcGreeter**. Ważne jest, aby nazwa projektu *GrpcGreeter* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.
* Wybierz pozycję **Utwórz**.
* W oknie dialogowym **Tworzenie nowej usługi gRPC** :
  * Wybrano szablon **usługi gRPC** .
  * Wybierz pozycję **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.
* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new`Polecenie tworzy nową usługę gRPC w folderze *GrpcGreeter* .
  * `code`Polecenie otwiera folder *GrpcGreeter* w nowym wystąpieniu Visual Studio Code.

  Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "GrpcGreeter". Dodać je?**
* Wybierz pozycję **Tak**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W terminalu uruchom następujące polecenia:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Poprzednie polecenia używają [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools/dotnet) do tworzenia usługi gRPC.

### <a name="open-the-project"></a>Otwórz projekt

W programie Visual Studio wybierz pozycję **plik**  >  **Otwórz**, a następnie wybierz plik *GrpcGreeter. csproj* .

---

### <a name="run-the-service"></a>Uruchamianie usługi

  [!INCLUDE[](~/includes/run-the-app.md)]

Dzienniki pokazują, że usługa nasłuchuje `https://localhost:5001` .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Szablon gRPC jest skonfigurowany do korzystania z [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246). Klienci gRPC muszą używać protokołu HTTPS, aby wywołać serwer.
>
> Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS. Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

*GrpcGreeter* pliki projektu:

* *Greeting. proto*: plik *Protos/Greeting. proto* definiuje `Greeter` gRPC i służy do generowania zasobów serwera gRPC. Aby uzyskać więcej informacji, zobacz [wprowadzenie do gRPC](xref:grpc/index).
* Folder *usługi* : zawiera implementację `Greeter` usługi.
* *appSettings.js*: zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.
* *Program.cs*: zawiera punkt wejścia dla usługi gRPC. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.
* *Startup.cs*: zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Tworzenie klienta gRPC w aplikacji konsolowej .NET

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Otwórz drugie wystąpienie programu Visual Studio i wybierz pozycję **Utwórz nowy projekt**.
* W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Core)** , a następnie wybierz pozycję **dalej**.
* W polu tekstowym **Nazwa projektu** wprowadź **GrpcGreeterClient** i wybierz pozycję **Utwórz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.
* Uruchom następujące polecenia:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Postępuj zgodnie z instrukcjami w temacie [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) , aby utworzyć aplikację konsolową o nazwie *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Dodaj wymagane pakiety

Projekt klienta gRPC wymaga następujących pakietów:

* [GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.
* [Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API komunikatów protobuf dla języka C#.
* [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/), która zawiera obsługę narzędzi C# dla plików protobuf. Pakiet narzędzi nie jest wymagany w czasie wykonywania, dlatego zależność jest oznaczona za pomocą `PrivateAssets="All"` .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub Zarządzaj pakietami NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opcja PMC, aby zainstalować pakiety

* W programie Visual Studio wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**
* W oknie **konsola Menedżera pakietów** Uruchom polecenie, `cd GrpcGreeterClient` Aby zmienić katalogi na folder zawierający pliki *GrpcGreeterClient. csproj* .
* Uruchom następujące polecenia:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Opcja zarządzania pakietami NuGet w celu zainstalowania pakietów

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**  >  **Zarządzanie pakietami NuGet**
* Wybierz kartę **Przeglądaj**.
* W polu wyszukiwania wprowadź **GRPC .NET. Client** .
* Wybierz pakiet **GRPC .NET. Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.
* Powtórz dla `Google.Protobuf` i `Grpc.Tools` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenia w **zintegrowanym terminalu**:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder **pakiety** w **okienko rozwiązania**  >  **Dodaj pakiety**
* W polu wyszukiwania wprowadź **GRPC .NET. Client** .
* Wybierz pakiet **GRPC .NET. Client** z okienka wyników, a następnie wybierz pozycję **Dodaj pakiet** .
* Powtórz dla `Google.Protobuf` i `Grpc.Tools` .

---

### <a name="add-greetproto"></a>Dodaj Greeting. proto

* Utwórz folder *Protos* w projekcie klienta gRPC.
* Skopiuj plik *Protos\greet.proto* z usługi gRPC Greeter do projektu klienta gRPC.
* Edytuj plik projektu *GrpcGreeterClient. csproj* :

  # <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

  Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Wybierz plik *GrpcGreeterClient. csproj* .

  # <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Narzędzia**  >  **Edytuj plik**.

  ---

* Dodaj grupę elementów z `<Protobuf>` elementem, który odwołuje się do pliku *Greeting. proto* :

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Tworzenie klienta Greeter

Skompiluj projekt, aby utworzyć typy w `GrpcGreeter` przestrzeni nazw. `GrpcGreeter`Typy są generowane automatycznie przez proces kompilacji.

Zaktualizuj plik *program.cs* klienta gRPC za pomocą następującego kodu:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.

Klient Greeter jest tworzony przez:

* Utworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.
* Korzystanie z programu `GrpcChannel` do konstruowania klienta Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Klient Greeter wywołuje metodę asynchroniczną `SayHello` . `SayHello`Zostanie wyświetlony wynik wywołania:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testowanie klienta gRPC za pomocą usługi gRPC Greeter

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W usłudze Greeter naciśnij klawisz, `Ctrl+F5` Aby uruchomić serwer bez debugera.
* W `GrpcGreeterClient` projekcie naciśnij klawisz, `Ctrl+F5` Aby uruchomić klienta bez debugera.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Uruchom usługę Greeter.
* Uruchom klienta programu.


# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Uruchom usługę Greeter.
* Uruchom klienta programu.

---

Klient wysyła do usługi powitanie z komunikatem zawierającym nazwę *GreeterClient*. Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź. Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach, które zostały zapisane w wierszu polecenia:

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
> Kod w tym artykule wymaga certyfikatu programistycznego HTTPS ASP.NET Core do zabezpieczenia usługi gRPC. Jeśli klient .NET gRPC kończy się niepowodzeniem z komunikatem `The remote certificate is invalid according to the validation procedure.` lub `The SSL connection could not be established.` , certyfikat programistyczny nie jest zaufany. Aby rozwiązać ten problem, zobacz [Wywoływanie usługi gRPC z niezaufanym/nieprawidłowym certyfikatem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Następne kroki

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
