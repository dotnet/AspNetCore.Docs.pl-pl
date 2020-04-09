---
page_type: sample
description: W tym samouczku pokazano, jak utworzyć usługę gRPC i klienta gRPC na ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660931"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Tworzenie klienta i serwera gRPC w ASP.NET Core 3.0 przy użyciu programu Visual Studio

W tym samouczku pokazano, jak utworzyć klienta [gRPC](https://grpc.io/docs/guides/) .NET Core i ASP.NET Core gRPC Server.

Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.

W tym samouczku, ty;

* Utwórz serwer gRPC.
* Utwórz klienta gRPC.
* Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.

## <a name="create-a-grpc-service"></a>Tworzenie usługi gRPC

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.
* W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **ASP.NET Podstawowa aplikacja sieci Web**.
* Wybierz pozycję **Dalej**
* Nazwij projekt **GrpcGreeter**. Ważne jest, aby nadać nazwę projektowi *GrpcGreeter,* aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.
* Wybierz **pozycję Utwórz**
* W oknie **dialogowym Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web:**
  * Wybierz **.NET Core** i **ASP.NET Core 3.0** w menu rozwijanym. 
  * Wybierz szablon **usługi gRPC.**
  * Wybierz **pozycję Utwórz**

### <a name="run-the-service"></a>Uruchamianie usługi

* Naciśnij, `Ctrl+F5` aby uruchomić usługę gRPC bez debugera.

  Visual Studio uruchamia usługę w wierszu polecenia.

Dzienniki pokazują, że `https://localhost:5001`usługa nasłuchuje .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> Szablon gRPC jest skonfigurowany do [używania zabezpieczeń warstwy transportowej (TLS).](https://tools.ietf.org/html/rfc5246) Klienci gRPC muszą używać protokołu HTTPS do wywoływania serwera.
>
> system macOS nie obsługuje ASP.NET core gRPC z TLS. Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS. Aby uzyskać więcej informacji, zobacz [gRPC i ASP.NET Core w systemie macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Sprawdzanie plików projektu

Pliki projektu *GrpcGreeter:*

* *greet.proto*: Plik *Protos/greet.proto* `Greeter` definiuje gRPC i jest używany do generowania zasobów serwera gRPC. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do gRPC](xref:grpc/index).
* *Folder usług:* Zawiera implementację `Greeter` usługi.
* *appSettings.json*: Zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.
* *Program.cs*: Zawiera punkt wejścia dla usługi gRPC. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Zawiera kod, który konfiguruje zachowanie aplikacji. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Tworzenie klienta gRPC w aplikacji konsoli .NET

* Otwórz drugie wystąpienie programu Visual Studio.
* Z paska menu **wybierz pozycję Plik** > **nowy** > **projekt.**
* W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **Aplikacja konsoli (.NET Core).**
* Wybierz pozycję **Dalej**
* W polu **tekstowym Nazwa** wpisz "GrpcGreeterClient".
* Wybierz **pozycję Utwórz**.

### <a name="add-required-packages"></a>Dodawanie wymaganych pakietów

Projekt klienta gRPC wymaga następujących pakietów:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API wiadomości protobuf dla języka C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), który zawiera c# obsługa narzędzi dla plików protobuf. Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest oznaczona `PrivateAssets="All"`.

Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub zarządzania pakietami NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opcja instalacji pakietów PMC

* W programie Visual Studio wybierz pozycję **Konsola** > Menedżera > **pakietów** **programu Narzędzia NuGet Package**Manager
* W oknie **Konsola Menedżera pakietów** przejdź do katalogu, w którym istnieje plik *GrpcGreeterClient.csproj.*
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

### <a name="add-greetproto"></a>Dodaj greet.proto

* Utwórz folder **Protos** w projekcie klienta gRPC.
* Skopiuj plik **Protos\greet.proto** z usługi gRPC Greeter do projektu klienta gRPC.
* Edytuj plik projektu *GrpcGreeterClient.csproj:*

  Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.

* Dodaj grupę elementów z elementem, `<Protobuf>` który odwołuje się do pliku **greet.proto:**

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Tworzenie klienta greeter

Skompiluj projekt, `GrpcGreeter` aby utworzyć typy w obszarze nazw. Typy `GrpcGreeter` są generowane automatycznie przez proces kompilacji.

Zaktualizuj plik *Program.cs* klienta gRPC za pomocą następującego kodu:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.

Klient Greeter jest tworzony przez:

* Tworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.
* Za `GrpcChannel` pomocą do konstruowania klienta Greeter.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Przetestuj klienta gRPC za pomocą usługi gRPC Greeter

* W usłudze Greeter `Ctrl+F5` naciśnij, aby uruchomić serwer bez debugera.
* W `GrpcGreeterClient` projekcie naciśnij, `Ctrl+F5` aby uruchomić klienta bez debugera.

Klient wysyła powitanie do usługi z komunikatem zawierającym jego nazwę "GreeterClient". Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź. Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach zapisanych w wierszu polecenia.

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

### <a name="docs-help--next-steps-for-grpc"></a>Dokumenty pomagają & kolejne kroki dla gRPC

* [Wprowadzenie do gRPC na ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [Usługi gRPC w środowisku C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrowanie usług gRPC z C-core do ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
