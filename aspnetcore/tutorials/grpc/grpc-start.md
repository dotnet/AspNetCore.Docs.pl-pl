---
title: Tworzenie klienta i serwera platformy .NET Core gRPC w ASP.NET Core
author: juntaoluo
description: W tym samouczku pokazano, jak utworzyć usługę gRPC i klienta gRPC na ASP.NET Core. Dowiedz się, jak utworzyć projekt usługi gRPC, edytować plik PROTO i dodać wywołanie przesyłania strumieniowego dupleksu.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424259"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="cfef5-104">Samouczek: Tworzenie gRPC klienta i serwera w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfef5-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="cfef5-105">Przez [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="cfef5-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="cfef5-106">W tym samouczku pokazano, jak utworzyć klienta programu .NET Core [gRPC](https://grpc.io/docs/guides/) oraz serwer gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfef5-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="cfef5-107">Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="cfef5-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="cfef5-108">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cfef5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cfef5-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="cfef5-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cfef5-110">Utwórz serwer gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="cfef5-111">Utwórz klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="cfef5-112">Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="cfef5-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cfef5-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cfef5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfef5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="cfef5-117">Tworzenie usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="cfef5-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfef5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfef5-119">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="cfef5-120">Alternatywnie z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="cfef5-121">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **Usługa gRPC** i wybierz pozycję **dalej**:</span><span class="sxs-lookup"><span data-stu-id="cfef5-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Okno dialogowe Tworzenie nowego projektu](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="cfef5-123">Nazwij projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="cfef5-124">Ważne jest, aby nazwa projektu *GrpcGreeter* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="cfef5-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="cfef5-125">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-125">Select **Create**.</span></span>
* <span data-ttu-id="cfef5-126">W oknie dialogowym **Tworzenie nowej usługi gRPC** :</span><span class="sxs-lookup"><span data-stu-id="cfef5-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="cfef5-127">Wybrano szablon **usługi gRPC** .</span><span class="sxs-lookup"><span data-stu-id="cfef5-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="cfef5-128">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cfef5-130">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cfef5-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cfef5-131">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="cfef5-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="cfef5-132">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="cfef5-133">`dotnet new`Polecenie tworzy nową usługę gRPC w folderze *GrpcGreeter* .</span><span class="sxs-lookup"><span data-stu-id="cfef5-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="cfef5-134">`code`Polecenie otwiera folder *GrpcGreeter* w nowym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="cfef5-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="cfef5-135">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "GrpcGreeter". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="cfef5-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="cfef5-136">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cfef5-138">W terminalu uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="cfef5-139">Poprzednie polecenia używają [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools/dotnet) do tworzenia usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="cfef5-140">Otwórz projekt</span><span class="sxs-lookup"><span data-stu-id="cfef5-140">Open the project</span></span>

<span data-ttu-id="cfef5-141">W programie Visual Studio wybierz pozycję **plik**  >  **Otwórz**, a następnie wybierz plik *GrpcGreeter. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cfef5-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="cfef5-142">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="cfef5-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="cfef5-143">Dzienniki pokazują, że usługa nasłuchuje `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="cfef5-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="cfef5-144">Szablon gRPC jest skonfigurowany do korzystania z [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="cfef5-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="cfef5-145">Klienci gRPC muszą używać protokołu HTTPS, aby wywołać serwer.</span><span class="sxs-lookup"><span data-stu-id="cfef5-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="cfef5-146">Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="cfef5-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="cfef5-147">Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="cfef5-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="cfef5-148">Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="cfef5-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="cfef5-149">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="cfef5-149">Examine the project files</span></span>

<span data-ttu-id="cfef5-150">*GrpcGreeter* pliki projektu:</span><span class="sxs-lookup"><span data-stu-id="cfef5-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="cfef5-151">*Greeting. proto*: plik *Protos/Greeting. proto* definiuje `Greeter` gRPC i służy do generowania zasobów serwera gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="cfef5-152">Aby uzyskać więcej informacji, zobacz [wprowadzenie do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="cfef5-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="cfef5-153">Folder *usługi* : zawiera implementację `Greeter` usługi.</span><span class="sxs-lookup"><span data-stu-id="cfef5-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="cfef5-154">*appSettings.js*: zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cfef5-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="cfef5-155">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cfef5-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="cfef5-156">*Program.cs*: zawiera punkt wejścia dla usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="cfef5-157">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cfef5-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="cfef5-158">*Startup.cs*: zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cfef5-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="cfef5-159">Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cfef5-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="cfef5-160">Tworzenie klienta gRPC w aplikacji konsolowej .NET</span><span class="sxs-lookup"><span data-stu-id="cfef5-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfef5-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfef5-162">Otwórz drugie wystąpienie programu Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="cfef5-163">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Core)** , a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="cfef5-164">W polu tekstowym **Nazwa projektu** wprowadź **GrpcGreeterClient** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-164">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cfef5-166">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="cfef5-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="cfef5-167">Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.</span><span class="sxs-lookup"><span data-stu-id="cfef5-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="cfef5-168">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-169">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cfef5-170">Postępuj zgodnie z instrukcjami w temacie [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) , aby utworzyć aplikację konsolową o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="cfef5-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="cfef5-171">Dodaj wymagane pakiety</span><span class="sxs-lookup"><span data-stu-id="cfef5-171">Add required packages</span></span>

<span data-ttu-id="cfef5-172">Projekt klienta gRPC wymaga następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="cfef5-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="cfef5-173">[GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfef5-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="cfef5-174">[Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API komunikatów protobuf dla języka C#.</span><span class="sxs-lookup"><span data-stu-id="cfef5-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="cfef5-175">[GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/), która zawiera obsługę narzędzi C# dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="cfef5-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="cfef5-176">Pakiet narzędzi nie jest wymagany w czasie wykonywania, dlatego zależność jest oznaczona za pomocą `PrivateAssets="All"` .</span><span class="sxs-lookup"><span data-stu-id="cfef5-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfef5-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cfef5-178">Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub Zarządzaj pakietami NuGet.</span><span class="sxs-lookup"><span data-stu-id="cfef5-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="cfef5-179">Opcja PMC, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="cfef5-179">PMC option to install packages</span></span>

* <span data-ttu-id="cfef5-180">W programie Visual Studio wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="cfef5-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="cfef5-181">W oknie **konsola Menedżera pakietów** Uruchom polecenie, `cd GrpcGreeterClient` Aby zmienić katalogi na folder zawierający pliki *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cfef5-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="cfef5-182">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="cfef5-183">Opcja zarządzania pakietami NuGet w celu zainstalowania pakietów</span><span class="sxs-lookup"><span data-stu-id="cfef5-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="cfef5-184">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**  >  **Zarządzanie pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="cfef5-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="cfef5-185">Wybierz kartę **Przeglądaj**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="cfef5-186">W polu wyszukiwania wprowadź **GRPC .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="cfef5-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="cfef5-187">Wybierz pakiet **GRPC .NET. Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="cfef5-188">Powtórz dla `Google.Protobuf` i `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="cfef5-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cfef5-190">Uruchom następujące polecenia w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="cfef5-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-191">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cfef5-192">Kliknij prawym przyciskiem myszy folder **pakiety** w **okienko rozwiązania**  >  **Dodaj pakiety**</span><span class="sxs-lookup"><span data-stu-id="cfef5-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="cfef5-193">W polu wyszukiwania wprowadź **GRPC .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="cfef5-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="cfef5-194">Wybierz pakiet **GRPC .NET. Client** z okienka wyników, a następnie wybierz pozycję **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="cfef5-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="cfef5-195">Powtórz dla `Google.Protobuf` i `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="cfef5-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="cfef5-196">Dodaj Greeting. proto</span><span class="sxs-lookup"><span data-stu-id="cfef5-196">Add greet.proto</span></span>

* <span data-ttu-id="cfef5-197">Utwórz folder *Protos* w projekcie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="cfef5-198">Skopiuj plik *Protos\greet.proto* z usługi gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="cfef5-199">Zaktualizuj przestrzeń nazw wewnątrz `greet.proto` pliku do przestrzeni nazw projektu:</span><span class="sxs-lookup"><span data-stu-id="cfef5-199">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="cfef5-200">Edytuj plik projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="cfef5-200">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="cfef5-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-201">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="cfef5-202">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-202">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="cfef5-204">Wybierz plik *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cfef5-204">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-205">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="cfef5-206">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Narzędzia**  >  **Edytuj plik**.</span><span class="sxs-lookup"><span data-stu-id="cfef5-206">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="cfef5-207">Dodaj grupę elementów z `<Protobuf>` elementem, który odwołuje się do pliku *Greeting. proto* :</span><span class="sxs-lookup"><span data-stu-id="cfef5-207">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="cfef5-208">Tworzenie klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="cfef5-208">Create the Greeter client</span></span>

<span data-ttu-id="cfef5-209">Skompiluj projekt, aby utworzyć typy w `GrpcGreeter` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="cfef5-209">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="cfef5-210">`GrpcGreeter`Typy są generowane automatycznie przez proces kompilacji.</span><span class="sxs-lookup"><span data-stu-id="cfef5-210">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="cfef5-211">Zaktualizuj plik *program.cs* klienta gRPC za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="cfef5-211">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="cfef5-212">*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-212">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="cfef5-213">Klient Greeter jest tworzony przez:</span><span class="sxs-lookup"><span data-stu-id="cfef5-213">The Greeter client is created by:</span></span>

* <span data-ttu-id="cfef5-214">Utworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-214">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="cfef5-215">Korzystanie z programu `GrpcChannel` do konstruowania klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="cfef5-215">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="cfef5-216">Klient Greeter wywołuje metodę asynchroniczną `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="cfef5-216">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="cfef5-217">`SayHello`Zostanie wyświetlony wynik wywołania:</span><span class="sxs-lookup"><span data-stu-id="cfef5-217">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="cfef5-218">Testowanie klienta gRPC za pomocą usługi gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="cfef5-218">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cfef5-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cfef5-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cfef5-220">W usłudze Greeter naciśnij klawisz, `Ctrl+F5` Aby uruchomić serwer bez debugera.</span><span class="sxs-lookup"><span data-stu-id="cfef5-220">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="cfef5-221">W `GrpcGreeterClient` projekcie naciśnij klawisz, `Ctrl+F5` Aby uruchomić klienta bez debugera.</span><span class="sxs-lookup"><span data-stu-id="cfef5-221">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cfef5-222">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cfef5-222">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cfef5-223">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="cfef5-223">Start the Greeter service.</span></span>
* <span data-ttu-id="cfef5-224">Uruchom klienta programu.</span><span class="sxs-lookup"><span data-stu-id="cfef5-224">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cfef5-225">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="cfef5-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cfef5-226">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="cfef5-226">Start the Greeter service.</span></span>
* <span data-ttu-id="cfef5-227">Uruchom klienta programu.</span><span class="sxs-lookup"><span data-stu-id="cfef5-227">Start the client.</span></span>

---

<span data-ttu-id="cfef5-228">Klient wysyła do usługi powitanie z komunikatem zawierającym nazwę *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="cfef5-228">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="cfef5-229">Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="cfef5-229">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="cfef5-230">Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-230">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="cfef5-231">Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach, które zostały zapisane w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="cfef5-231">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="cfef5-232">Kod w tym artykule wymaga certyfikatu programistycznego HTTPS ASP.NET Core do zabezpieczenia usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="cfef5-232">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="cfef5-233">Jeśli klient .NET gRPC kończy się niepowodzeniem z komunikatem `The remote certificate is invalid according to the validation procedure.` lub `The SSL connection could not be established.` , certyfikat programistyczny nie jest zaufany.</span><span class="sxs-lookup"><span data-stu-id="cfef5-233">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="cfef5-234">Aby rozwiązać ten problem, zobacz [Wywoływanie usługi gRPC z niezaufanym/nieprawidłowym certyfikatem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="cfef5-234">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="cfef5-235">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="cfef5-235">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
