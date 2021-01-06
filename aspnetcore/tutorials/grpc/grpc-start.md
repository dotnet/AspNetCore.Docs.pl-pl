---
title: Tworzenie klienta i serwera platformy .NET Core gRPC w ASP.NET Core
author: juntaoluo
description: W tym samouczku pokazano, jak utworzyć usługę gRPC i klienta gRPC na ASP.NET Core. Dowiedz się, jak utworzyć projekt usługi gRPC, edytować plik PROTO i dodać wywołanie przesyłania strumieniowego dupleksu.
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057027"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="05f88-104">Samouczek: Tworzenie gRPC klienta i serwera w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05f88-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="05f88-105">Przez [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="05f88-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="05f88-106">W tym samouczku pokazano, jak utworzyć klienta programu .NET Core [gRPC](https://grpc.io/docs/guides/) oraz serwer gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="05f88-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="05f88-107">Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="05f88-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="05f88-108">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="05f88-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="05f88-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="05f88-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="05f88-110">Utwórz serwer gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="05f88-111">Utwórz klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="05f88-112">Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="05f88-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05f88-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="05f88-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05f88-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="05f88-117">Visual Studio dla komputerów Mac wersja 8,7 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="05f88-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="05f88-118">Tworzenie usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="05f88-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05f88-119">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05f88-120">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="05f88-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="05f88-121">Alternatywnie z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="05f88-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="05f88-122">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **Usługa gRPC** i wybierz pozycję **dalej**:</span><span class="sxs-lookup"><span data-stu-id="05f88-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Tworzenie okna dialogowego nowego projektu w programie Visual Studio](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="05f88-124">Nazwij projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="05f88-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="05f88-125">Ważne jest, aby nazwa projektu *GrpcGreeter* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="05f88-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="05f88-126">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05f88-126">Select **Create**.</span></span>
* <span data-ttu-id="05f88-127">W oknie dialogowym **Tworzenie nowej usługi gRPC** :</span><span class="sxs-lookup"><span data-stu-id="05f88-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="05f88-128">Wybrano szablon **usługi gRPC** .</span><span class="sxs-lookup"><span data-stu-id="05f88-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="05f88-129">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05f88-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05f88-131">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="05f88-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="05f88-132">Zmień katalogi ( `cd` ) na folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="05f88-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="05f88-133">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05f88-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="05f88-134">`dotnet new`Polecenie tworzy nową usługę gRPC w folderze *GrpcGreeter* .</span><span class="sxs-lookup"><span data-stu-id="05f88-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="05f88-135">`code`Polecenie otwiera folder *GrpcGreeter* w nowym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="05f88-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="05f88-136">Zostanie wyświetlone okno dialogowe z **wymaganymi zasobami do kompilowania i debugowania brakuje w "GrpcGreeter". Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="05f88-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="05f88-137">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="05f88-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-138">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05f88-139">Rozpocznij Visual Studio dla komputerów Mac i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="05f88-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="05f88-140">Alternatywnie z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="05f88-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="05f88-141">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję Usługa **sieci Web i**  >  **aplikacja** konsoli  >  **gRPC** i wybierz pozycję **dalej**:</span><span class="sxs-lookup"><span data-stu-id="05f88-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next**:</span></span>

  ![Utwórz nowe okno dialogowe projektu w witrynie macOS](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="05f88-143">Wybierz pozycję **.NET Core 3,1** dla platformy docelowej i wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="05f88-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="05f88-144">Nazwij projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="05f88-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="05f88-145">Ważne jest, aby nazwa projektu *GrpcGreeter* , tak aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="05f88-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="05f88-146">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05f88-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="05f88-147">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="05f88-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="05f88-148">Dzienniki pokazują, że usługa nasłuchuje `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="05f88-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="05f88-149">Szablon gRPC jest skonfigurowany do korzystania z [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="05f88-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="05f88-150">Klienci gRPC muszą używać protokołu HTTPS, aby wywołać serwer.</span><span class="sxs-lookup"><span data-stu-id="05f88-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="05f88-151">Program macOS nie obsługuje ASP.NET Core gRPC z protokołem TLS.</span><span class="sxs-lookup"><span data-stu-id="05f88-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="05f88-152">Aby pomyślnie uruchomić usługi gRPC Services w usłudze macOS, wymagana jest dodatkowa konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="05f88-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="05f88-153">Aby uzyskać więcej informacji, zobacz [nie można uruchomić aplikacji ASP.NET Core gRPC w witrynie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="05f88-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="05f88-154">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="05f88-154">Examine the project files</span></span>

<span data-ttu-id="05f88-155">*GrpcGreeter* pliki projektu:</span><span class="sxs-lookup"><span data-stu-id="05f88-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="05f88-156">*Greeting. proto*: plik *Protos/Greeting. proto* definiuje `Greeter` gRPC i służy do generowania zasobów serwera gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="05f88-157">Aby uzyskać więcej informacji, zobacz [wprowadzenie do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="05f88-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="05f88-158">Folder *usługi* : zawiera implementację `Greeter` usługi.</span><span class="sxs-lookup"><span data-stu-id="05f88-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="05f88-159">*appSettings.js*: zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="05f88-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="05f88-160">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="05f88-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="05f88-161">*Program.cs*: zawiera punkt wejścia dla usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="05f88-162">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="05f88-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="05f88-163">*Startup.cs*: zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="05f88-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="05f88-164">Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="05f88-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="05f88-165">Tworzenie klienta gRPC w aplikacji konsolowej .NET</span><span class="sxs-lookup"><span data-stu-id="05f88-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05f88-166">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05f88-167">Otwórz drugie wystąpienie programu Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="05f88-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="05f88-168">W oknie dialogowym **Tworzenie nowego projektu** wybierz pozycję **aplikacja konsoli (.NET Core)** , a następnie wybierz pozycję **dalej**.</span><span class="sxs-lookup"><span data-stu-id="05f88-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="05f88-169">W polu tekstowym **Nazwa projektu** wprowadź **GrpcGreeterClient** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="05f88-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05f88-171">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="05f88-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="05f88-172">Zmień katalogi ( `cd` ) na folder dla projektu.</span><span class="sxs-lookup"><span data-stu-id="05f88-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="05f88-173">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05f88-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-174">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="05f88-175">Postępuj zgodnie z instrukcjami w temacie [Tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu Visual Studio dla komputerów Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) , aby utworzyć aplikację konsolową o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="05f88-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="05f88-176">Dodaj wymagane pakiety</span><span class="sxs-lookup"><span data-stu-id="05f88-176">Add required packages</span></span>

<span data-ttu-id="05f88-177">Projekt klienta gRPC wymaga następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="05f88-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="05f88-178">[GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="05f88-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="05f88-179">[Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API komunikatów protobuf dla języka C#.</span><span class="sxs-lookup"><span data-stu-id="05f88-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="05f88-180">[GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/), która zawiera obsługę narzędzi C# dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="05f88-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="05f88-181">Pakiet narzędzi nie jest wymagany w czasie wykonywania, dlatego zależność jest oznaczona za pomocą `PrivateAssets="All"` .</span><span class="sxs-lookup"><span data-stu-id="05f88-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05f88-182">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05f88-183">Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub Zarządzaj pakietami NuGet.</span><span class="sxs-lookup"><span data-stu-id="05f88-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="05f88-184">Opcja PMC, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="05f88-184">PMC option to install packages</span></span>

* <span data-ttu-id="05f88-185">W programie Visual Studio wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**</span><span class="sxs-lookup"><span data-stu-id="05f88-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="05f88-186">W oknie **konsola Menedżera pakietów** Uruchom polecenie, `cd GrpcGreeterClient` Aby zmienić katalogi na folder zawierający pliki *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="05f88-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="05f88-187">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="05f88-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="05f88-188">Opcja zarządzania pakietami NuGet w celu zainstalowania pakietów</span><span class="sxs-lookup"><span data-stu-id="05f88-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="05f88-189">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**  >  **zarządzać pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="05f88-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="05f88-190">Wybierz kartę **Przeglądaj**.</span><span class="sxs-lookup"><span data-stu-id="05f88-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="05f88-191">W polu wyszukiwania wprowadź **GRPC .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="05f88-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="05f88-192">Wybierz pakiet **GRPC .NET. Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="05f88-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="05f88-193">Powtórz dla `Google.Protobuf` i `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="05f88-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="05f88-195">Uruchom następujące polecenia w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="05f88-195">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-196">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05f88-197">Kliknij prawym przyciskiem myszy projekt **GrpcGreeterClient** w **okienko rozwiązania** i wybierz pozycję **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="05f88-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="05f88-198">W polu wyszukiwania wprowadź **GRPC .NET. Client** .</span><span class="sxs-lookup"><span data-stu-id="05f88-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="05f88-199">Wybierz pakiet **GRPC .NET. Client** z okienka wyników, a następnie wybierz pozycję **Dodaj pakiet**.</span><span class="sxs-lookup"><span data-stu-id="05f88-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="05f88-200">Wybierz przycisk **Akceptuj** w oknie dialogowym **akceptowanie licencji** .</span><span class="sxs-lookup"><span data-stu-id="05f88-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="05f88-201">Powtórz dla `Google.Protobuf` i `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="05f88-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="05f88-202">Dodaj Greeting. proto</span><span class="sxs-lookup"><span data-stu-id="05f88-202">Add greet.proto</span></span>

* <span data-ttu-id="05f88-203">Utwórz folder *Protos* w projekcie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="05f88-204">Skopiuj plik *Protos\greet.proto* z usługi gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="05f88-205">Zaktualizuj przestrzeń nazw wewnątrz `greet.proto` pliku do przestrzeni nazw projektu:</span><span class="sxs-lookup"><span data-stu-id="05f88-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="05f88-206">Edytuj plik projektu *GrpcGreeterClient. csproj* :</span><span class="sxs-lookup"><span data-stu-id="05f88-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="05f88-207">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="05f88-208">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.</span><span class="sxs-lookup"><span data-stu-id="05f88-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="05f88-210">Wybierz plik *GrpcGreeterClient. csproj* .</span><span class="sxs-lookup"><span data-stu-id="05f88-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-211">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="05f88-212">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.</span><span class="sxs-lookup"><span data-stu-id="05f88-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="05f88-213">Dodaj grupę elementów z `<Protobuf>` elementem, który odwołuje się do pliku *Greeting. proto* :</span><span class="sxs-lookup"><span data-stu-id="05f88-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="05f88-214">Tworzenie klienta Greeter</span><span class="sxs-lookup"><span data-stu-id="05f88-214">Create the Greeter client</span></span>

<span data-ttu-id="05f88-215">Skompiluj projekt klienta, aby utworzyć typy w `GrpcGreeter` przestrzeni nazw.</span><span class="sxs-lookup"><span data-stu-id="05f88-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="05f88-216">`GrpcGreeter`Typy są generowane automatycznie przez proces kompilacji.</span><span class="sxs-lookup"><span data-stu-id="05f88-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="05f88-217">Zaktualizuj plik *program.cs* klienta gRPC za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="05f88-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="05f88-218">*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="05f88-219">Klient Greeter jest tworzony przez:</span><span class="sxs-lookup"><span data-stu-id="05f88-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="05f88-220">Utworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="05f88-221">Korzystanie z programu `GrpcChannel` do konstruowania klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="05f88-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="05f88-222">Klient Greeter wywołuje metodę asynchroniczną `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="05f88-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="05f88-223">`SayHello`Zostanie wyświetlony wynik wywołania:</span><span class="sxs-lookup"><span data-stu-id="05f88-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="05f88-224">Testowanie klienta gRPC za pomocą usługi gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="05f88-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05f88-225">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05f88-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05f88-226">W usłudze Greeter naciśnij klawisz, `Ctrl+F5` Aby uruchomić serwer bez debugera.</span><span class="sxs-lookup"><span data-stu-id="05f88-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="05f88-227">W `GrpcGreeterClient` projekcie naciśnij klawisz, `Ctrl+F5` Aby uruchomić klienta bez debugera.</span><span class="sxs-lookup"><span data-stu-id="05f88-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="05f88-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="05f88-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="05f88-229">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="05f88-229">Start the Greeter service.</span></span>
* <span data-ttu-id="05f88-230">Uruchom klienta programu.</span><span class="sxs-lookup"><span data-stu-id="05f88-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="05f88-231">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="05f88-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="05f88-232">Ze względu na opisany wcześniej [problem z protokołem TLS protokołu HTTP/2 na macOS obejście problemu](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)należy zaktualizować adres kanału w kliencie do " http://localhost:5000 ".</span><span class="sxs-lookup"><span data-stu-id="05f88-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="05f88-233">Zaktualizuj wiersz 13 **GrpcGreeterClient/program. cs** do odczytu:</span><span class="sxs-lookup"><span data-stu-id="05f88-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="05f88-234">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="05f88-234">Start the Greeter service.</span></span>
* <span data-ttu-id="05f88-235">Uruchom klienta programu.</span><span class="sxs-lookup"><span data-stu-id="05f88-235">Start the client.</span></span>

---

<span data-ttu-id="05f88-236">Klient wysyła do usługi powitanie z komunikatem zawierającym nazwę *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="05f88-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="05f88-237">Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="05f88-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="05f88-238">Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="05f88-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="05f88-239">Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach, które zostały zapisane w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="05f88-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="05f88-240">Kod w tym artykule wymaga certyfikatu programistycznego HTTPS ASP.NET Core do zabezpieczenia usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="05f88-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="05f88-241">Jeśli klient .NET gRPC kończy się niepowodzeniem z komunikatem `The remote certificate is invalid according to the validation procedure.` lub `The SSL connection could not be established.` , certyfikat programistyczny nie jest zaufany.</span><span class="sxs-lookup"><span data-stu-id="05f88-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="05f88-242">Aby rozwiązać ten problem, zobacz [Wywoływanie usługi gRPC z niezaufanym/nieprawidłowym certyfikatem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="05f88-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="05f88-243">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="05f88-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
