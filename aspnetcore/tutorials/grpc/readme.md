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
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="9dfce-102">Tworzenie klienta i serwera gRPC w ASP.NET Core 3.0 przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9dfce-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="9dfce-103">W tym samouczku pokazano, jak utworzyć klienta [gRPC](https://grpc.io/docs/guides/) .NET Core i ASP.NET Core gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="9dfce-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="9dfce-104">Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="9dfce-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="9dfce-105">W tym samouczku, ty;</span><span class="sxs-lookup"><span data-stu-id="9dfce-105">In this tutorial, you;</span></span>

* <span data-ttu-id="9dfce-106">Utwórz serwer gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="9dfce-107">Utwórz klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-107">Create a gRPC client.</span></span>
* <span data-ttu-id="9dfce-108">Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="9dfce-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="9dfce-109">Tworzenie usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="9dfce-109">Create a gRPC service</span></span>

* <span data-ttu-id="9dfce-110">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9dfce-111">W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **ASP.NET Podstawowa aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9dfce-112">Wybierz pozycję **Dalej**</span><span class="sxs-lookup"><span data-stu-id="9dfce-112">Select **Next**</span></span>
* <span data-ttu-id="9dfce-113">Nazwij projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="9dfce-114">Ważne jest, aby nadać nazwę projektowi *GrpcGreeter,* aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="9dfce-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="9dfce-115">Wybierz **pozycję Utwórz**</span><span class="sxs-lookup"><span data-stu-id="9dfce-115">Select **Create**</span></span>
* <span data-ttu-id="9dfce-116">W oknie **dialogowym Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="9dfce-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="9dfce-117">Wybierz **.NET Core** i **ASP.NET Core 3.0** w menu rozwijanym.</span><span class="sxs-lookup"><span data-stu-id="9dfce-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="9dfce-118">Wybierz szablon **usługi gRPC.**</span><span class="sxs-lookup"><span data-stu-id="9dfce-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="9dfce-119">Wybierz **pozycję Utwórz**</span><span class="sxs-lookup"><span data-stu-id="9dfce-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="9dfce-120">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="9dfce-120">Run the service</span></span>

* <span data-ttu-id="9dfce-121">Naciśnij, `Ctrl+F5` aby uruchomić usługę gRPC bez debugera.</span><span class="sxs-lookup"><span data-stu-id="9dfce-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="9dfce-122">Visual Studio uruchamia usługę w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="9dfce-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="9dfce-123">Dzienniki pokazują, że `https://localhost:5001`usługa nasłuchuje .</span><span class="sxs-lookup"><span data-stu-id="9dfce-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="9dfce-124">Szablon gRPC jest skonfigurowany do [używania zabezpieczeń warstwy transportowej (TLS).](https://tools.ietf.org/html/rfc5246)</span><span class="sxs-lookup"><span data-stu-id="9dfce-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9dfce-125">Klienci gRPC muszą używać protokołu HTTPS do wywoływania serwera.</span><span class="sxs-lookup"><span data-stu-id="9dfce-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="9dfce-126">system macOS nie obsługuje ASP.NET core gRPC z TLS.</span><span class="sxs-lookup"><span data-stu-id="9dfce-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9dfce-127">Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS.</span><span class="sxs-lookup"><span data-stu-id="9dfce-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9dfce-128">Aby uzyskać więcej informacji, zobacz [gRPC i ASP.NET Core w systemie macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span><span class="sxs-lookup"><span data-stu-id="9dfce-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="9dfce-129">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="9dfce-129">Examine the project files</span></span>

<span data-ttu-id="9dfce-130">Pliki projektu *GrpcGreeter:*</span><span class="sxs-lookup"><span data-stu-id="9dfce-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="9dfce-131">*greet.proto*: Plik *Protos/greet.proto* `Greeter` definiuje gRPC i jest używany do generowania zasobów serwera gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="9dfce-132">Aby uzyskać więcej informacji, zobacz [Wprowadzenie do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="9dfce-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="9dfce-133">*Folder usług:* Zawiera implementację `Greeter` usługi.</span><span class="sxs-lookup"><span data-stu-id="9dfce-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="9dfce-134">*appSettings.json*: Zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9dfce-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="9dfce-135">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9dfce-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="9dfce-136">*Program.cs*: Zawiera punkt wejścia dla usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="9dfce-137">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9dfce-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="9dfce-138">*Startup.cs*: Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9dfce-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="9dfce-139">Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="9dfce-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="9dfce-140">Tworzenie klienta gRPC w aplikacji konsoli .NET</span><span class="sxs-lookup"><span data-stu-id="9dfce-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="9dfce-141">Otwórz drugie wystąpienie programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9dfce-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="9dfce-142">Z paska menu **wybierz pozycję Plik** > **nowy** > **projekt.**</span><span class="sxs-lookup"><span data-stu-id="9dfce-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="9dfce-143">W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **Aplikacja konsoli (.NET Core).**</span><span class="sxs-lookup"><span data-stu-id="9dfce-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="9dfce-144">Wybierz pozycję **Dalej**</span><span class="sxs-lookup"><span data-stu-id="9dfce-144">Select **Next**</span></span>
* <span data-ttu-id="9dfce-145">W polu **tekstowym Nazwa** wpisz "GrpcGreeterClient".</span><span class="sxs-lookup"><span data-stu-id="9dfce-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="9dfce-146">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="9dfce-147">Dodawanie wymaganych pakietów</span><span class="sxs-lookup"><span data-stu-id="9dfce-147">Add required packages</span></span>

<span data-ttu-id="9dfce-148">Projekt klienta gRPC wymaga następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="9dfce-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="9dfce-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9dfce-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="9dfce-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API wiadomości protobuf dla języka C#.</span><span class="sxs-lookup"><span data-stu-id="9dfce-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9dfce-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), który zawiera c# obsługa narzędzi dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="9dfce-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9dfce-152">Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest oznaczona `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="9dfce-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="9dfce-153">Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub zarządzania pakietami NuGet.</span><span class="sxs-lookup"><span data-stu-id="9dfce-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9dfce-154">Opcja instalacji pakietów PMC</span><span class="sxs-lookup"><span data-stu-id="9dfce-154">PMC option to install packages</span></span>

* <span data-ttu-id="9dfce-155">W programie Visual Studio wybierz pozycję **Konsola** > Menedżera > **pakietów** **programu Narzędzia NuGet Package**Manager</span><span class="sxs-lookup"><span data-stu-id="9dfce-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9dfce-156">W oknie **Konsola Menedżera pakietów** przejdź do katalogu, w którym istnieje plik *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="9dfce-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="9dfce-157">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="9dfce-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9dfce-158">Zarządzanie pakietami NuGet, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="9dfce-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9dfce-159">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="9dfce-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="9dfce-160">Wybierz kartę **Przeglądaj**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="9dfce-161">Wprowadź **grpc.Net.Client** w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="9dfce-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9dfce-162">Wybierz pakiet **Grpc.Net.Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="9dfce-163">Powtarzanie `Google.Protobuf` `Grpc.Tools`dla i .</span><span class="sxs-lookup"><span data-stu-id="9dfce-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="9dfce-164">Dodaj greet.proto</span><span class="sxs-lookup"><span data-stu-id="9dfce-164">Add greet.proto</span></span>

* <span data-ttu-id="9dfce-165">Utwórz folder **Protos** w projekcie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="9dfce-166">Skopiuj plik **Protos\greet.proto** z usługi gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="9dfce-167">Edytuj plik projektu *GrpcGreeterClient.csproj:*</span><span class="sxs-lookup"><span data-stu-id="9dfce-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="9dfce-168">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.</span><span class="sxs-lookup"><span data-stu-id="9dfce-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="9dfce-169">Dodaj grupę elementów z elementem, `<Protobuf>` który odwołuje się do pliku **greet.proto:**</span><span class="sxs-lookup"><span data-stu-id="9dfce-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="9dfce-170">Tworzenie klienta greeter</span><span class="sxs-lookup"><span data-stu-id="9dfce-170">Create the Greeter client</span></span>

<span data-ttu-id="9dfce-171">Skompiluj projekt, `GrpcGreeter` aby utworzyć typy w obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="9dfce-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="9dfce-172">Typy `GrpcGreeter` są generowane automatycznie przez proces kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9dfce-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="9dfce-173">Zaktualizuj plik *Program.cs* klienta gRPC za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="9dfce-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="9dfce-174">*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="9dfce-175">Klient Greeter jest tworzony przez:</span><span class="sxs-lookup"><span data-stu-id="9dfce-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="9dfce-176">Tworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="9dfce-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="9dfce-177">Za `GrpcChannel` pomocą do konstruowania klienta Greeter.</span><span class="sxs-lookup"><span data-stu-id="9dfce-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9dfce-178">Przetestuj klienta gRPC za pomocą usługi gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="9dfce-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="9dfce-179">W usłudze Greeter `Ctrl+F5` naciśnij, aby uruchomić serwer bez debugera.</span><span class="sxs-lookup"><span data-stu-id="9dfce-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="9dfce-180">W `GrpcGreeterClient` projekcie naciśnij, `Ctrl+F5` aby uruchomić klienta bez debugera.</span><span class="sxs-lookup"><span data-stu-id="9dfce-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="9dfce-181">Klient wysyła powitanie do usługi z komunikatem zawierającym jego nazwę "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="9dfce-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="9dfce-182">Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="9dfce-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="9dfce-183">Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="9dfce-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9dfce-184">Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach zapisanych w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="9dfce-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="9dfce-185">Dokumenty pomagają & kolejne kroki dla gRPC</span><span class="sxs-lookup"><span data-stu-id="9dfce-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="9dfce-186">Wprowadzenie do gRPC na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dfce-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="9dfce-187">Usługi gRPC w środowisku C#</span><span class="sxs-lookup"><span data-stu-id="9dfce-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="9dfce-188">Migrowanie usług gRPC z C-core do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dfce-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
