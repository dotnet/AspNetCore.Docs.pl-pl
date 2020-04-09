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
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="b25fe-104">Samouczek: Tworzenie klienta i serwera gRPC w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b25fe-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="b25fe-105">Przez [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b25fe-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b25fe-106">W tym samouczku pokazano, jak utworzyć klienta [gRPC](https://grpc.io/docs/guides/) .NET Core i ASP.NET Core gRPC Server.</span><span class="sxs-lookup"><span data-stu-id="b25fe-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="b25fe-107">Na koniec będziesz mieć klienta gRPC, który komunikuje się z usługą gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="b25fe-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="b25fe-108">[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) [(jak pobrać).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b25fe-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b25fe-109">W tym samouczku zostały wykonane następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="b25fe-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b25fe-110">Utwórz serwer gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="b25fe-111">Utwórz klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="b25fe-112">Przetestuj usługę klienta gRPC za pomocą usługi gRPC Greeter.</span><span class="sxs-lookup"><span data-stu-id="b25fe-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b25fe-113">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b25fe-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b25fe-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-116">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="b25fe-117">Tworzenie usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="b25fe-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b25fe-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b25fe-119">Uruchom program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="b25fe-120">Alternatywnie z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b25fe-121">W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **gRPC Service** i wybierz pozycję **Dalej:**</span><span class="sxs-lookup"><span data-stu-id="b25fe-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Tworzenie nowego okna dialogowego projektu](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="b25fe-123">Nazwij projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="b25fe-124">Ważne jest, aby nadać nazwę projektowi *GrpcGreeter,* aby przestrzenie nazw były zgodne podczas kopiowania i wklejania kodu.</span><span class="sxs-lookup"><span data-stu-id="b25fe-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="b25fe-125">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-125">Select **Create**.</span></span>
* <span data-ttu-id="b25fe-126">W oknie **dialogowym Tworzenie nowej usługi gRPC:**</span><span class="sxs-lookup"><span data-stu-id="b25fe-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="b25fe-127">Zostanie wybrany szablon **usługi gRPC.**</span><span class="sxs-lookup"><span data-stu-id="b25fe-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="b25fe-128">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b25fe-130">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b25fe-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b25fe-131">Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="b25fe-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b25fe-132">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="b25fe-133">Polecenie `dotnet new` tworzy nową usługę gRPC w folderze *GrpcGreeter.*</span><span class="sxs-lookup"><span data-stu-id="b25fe-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="b25fe-134">Polecenie `code` otwiera folder *GrpcGreeter* w nowym wystąpieniu programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b25fe-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="b25fe-135">W "GrpcGreeter" pojawi się okno dialogowe z **wymaganymi zasobami do tworzenia i debugowania. Dodać je?**</span><span class="sxs-lookup"><span data-stu-id="b25fe-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="b25fe-136">Wybierz **pozycję Tak**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-137">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b25fe-138">Z terminala uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="b25fe-139">Poprzednie polecenia używają [interfejsu wiersza polecenia .NET Core](/dotnet/core/tools/dotnet) do utworzenia usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="b25fe-140">Otwieranie projektu</span><span class="sxs-lookup"><span data-stu-id="b25fe-140">Open the project</span></span>

<span data-ttu-id="b25fe-141">W programie Visual Studio wybierz pozycję**Otwieranie** **pliku** > , a następnie wybierz plik *GrpcGreeter.csproj.*</span><span class="sxs-lookup"><span data-stu-id="b25fe-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="b25fe-142">Uruchamianie usługi</span><span class="sxs-lookup"><span data-stu-id="b25fe-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="b25fe-143">Dzienniki pokazują, że `https://localhost:5001`usługa nasłuchuje .</span><span class="sxs-lookup"><span data-stu-id="b25fe-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="b25fe-144">Szablon gRPC jest skonfigurowany do [używania zabezpieczeń warstwy transportowej (TLS).](https://tools.ietf.org/html/rfc5246)</span><span class="sxs-lookup"><span data-stu-id="b25fe-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="b25fe-145">Klienci gRPC muszą używać protokołu HTTPS do wywoływania serwera.</span><span class="sxs-lookup"><span data-stu-id="b25fe-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="b25fe-146">system macOS nie obsługuje ASP.NET core gRPC z TLS.</span><span class="sxs-lookup"><span data-stu-id="b25fe-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="b25fe-147">Dodatkowa konfiguracja jest wymagana do pomyślnego uruchomienia usług gRPC w systemie macOS.</span><span class="sxs-lookup"><span data-stu-id="b25fe-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="b25fe-148">Aby uzyskać więcej informacji, zobacz [Nie można uruchomić aplikacji ASP.NET Core gRPC w systemie macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="b25fe-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="b25fe-149">Sprawdzanie plików projektu</span><span class="sxs-lookup"><span data-stu-id="b25fe-149">Examine the project files</span></span>

<span data-ttu-id="b25fe-150">Pliki projektu *GrpcGreeter:*</span><span class="sxs-lookup"><span data-stu-id="b25fe-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="b25fe-151">*greet.proto* &ndash; Plik *Protos/greet.proto* definiuje `Greeter` gRPC i jest używany do generowania aktywów serwera gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="b25fe-152">Aby uzyskać więcej informacji, zobacz [Wprowadzenie do gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="b25fe-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="b25fe-153">*Folder usług:* Zawiera implementację `Greeter` usługi.</span><span class="sxs-lookup"><span data-stu-id="b25fe-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="b25fe-154">*appSettings.json* &ndash; Zawiera dane konfiguracyjne, takie jak protokół używany przez Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b25fe-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="b25fe-155">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b25fe-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="b25fe-156">*Program.cs* &ndash; Zawiera punkt wejścia dla usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="b25fe-157">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="b25fe-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="b25fe-158">*Startup.cs* &ndash; Zawiera kod, który konfiguruje zachowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b25fe-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="b25fe-159">Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="b25fe-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="b25fe-160">Tworzenie klienta gRPC w aplikacji konsoli .NET</span><span class="sxs-lookup"><span data-stu-id="b25fe-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b25fe-161">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b25fe-162">Otwórz drugie wystąpienie programu Visual Studio i wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="b25fe-163">W oknie **dialogowym Tworzenie nowego projektu** wybierz pozycję **Aplikacja konsoli (.NET Core)** i wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="b25fe-164">W polu tekstowym **Nazwa** wprowadź **grpcGreeterClient** i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b25fe-166">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b25fe-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="b25fe-167">Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.</span><span class="sxs-lookup"><span data-stu-id="b25fe-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="b25fe-168">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-169">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b25fe-170">Postępuj zgodnie z instrukcjami w [tworzenie kompletnego rozwiązania .NET Core w systemie macOS przy użyciu programu Visual Studio dla komputerów Mac,](/dotnet/core/tutorials/using-on-mac-vs-full-solution) aby utworzyć aplikację konsoli o nazwie *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="b25fe-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="b25fe-171">Dodawanie wymaganych pakietów</span><span class="sxs-lookup"><span data-stu-id="b25fe-171">Add required packages</span></span>

<span data-ttu-id="b25fe-172">Projekt klienta gRPC wymaga następujących pakietów:</span><span class="sxs-lookup"><span data-stu-id="b25fe-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="b25fe-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), który zawiera klienta .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b25fe-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="b25fe-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), który zawiera interfejsy API wiadomości protobuf dla języka C#.</span><span class="sxs-lookup"><span data-stu-id="b25fe-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="b25fe-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), który zawiera c# obsługa narzędzi dla plików protobuf.</span><span class="sxs-lookup"><span data-stu-id="b25fe-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="b25fe-176">Pakiet narzędzi nie jest wymagany w czasie wykonywania, więc zależność jest oznaczona `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="b25fe-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b25fe-177">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b25fe-178">Zainstaluj pakiety przy użyciu konsoli Menedżera pakietów (PMC) lub zarządzania pakietami NuGet.</span><span class="sxs-lookup"><span data-stu-id="b25fe-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="b25fe-179">Opcja instalacji pakietów PMC</span><span class="sxs-lookup"><span data-stu-id="b25fe-179">PMC option to install packages</span></span>

* <span data-ttu-id="b25fe-180">W programie Visual Studio wybierz pozycję **Konsola** > Menedżera > **pakietów** **programu Narzędzia NuGet Package**Manager</span><span class="sxs-lookup"><span data-stu-id="b25fe-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="b25fe-181">W oknie **Konsola Menedżera pakietów** uruchom, `cd GrpcGreeterClient` aby zmienić katalogi do folderu zawierającego pliki *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="b25fe-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="b25fe-182">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="b25fe-183">Zarządzanie pakietami NuGet, aby zainstalować pakiety</span><span class="sxs-lookup"><span data-stu-id="b25fe-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="b25fe-184">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="b25fe-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="b25fe-185">Wybierz kartę **Przeglądaj**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="b25fe-186">Wprowadź **grpc.Net.Client** w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="b25fe-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="b25fe-187">Wybierz pakiet **Grpc.Net.Client** z karty **Przeglądaj** i wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="b25fe-188">Powtarzanie `Google.Protobuf` `Grpc.Tools`dla i .</span><span class="sxs-lookup"><span data-stu-id="b25fe-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b25fe-190">Uruchom następujące polecenia z **terminala zintegrowanego:**</span><span class="sxs-lookup"><span data-stu-id="b25fe-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-191">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b25fe-192">Kliknij prawym przyciskiem myszy folder **Pakiety** w **panelu rozrachowy** > **Dodawanie pakietów**</span><span class="sxs-lookup"><span data-stu-id="b25fe-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="b25fe-193">Wprowadź **grpc.Net.Client** w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="b25fe-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="b25fe-194">Wybierz pakiet **Grpc.Net.Client** z okienka wyników i wybierz **dodaj pakiet**</span><span class="sxs-lookup"><span data-stu-id="b25fe-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="b25fe-195">Powtarzanie `Google.Protobuf` `Grpc.Tools`dla i .</span><span class="sxs-lookup"><span data-stu-id="b25fe-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="b25fe-196">Dodaj greet.proto</span><span class="sxs-lookup"><span data-stu-id="b25fe-196">Add greet.proto</span></span>

* <span data-ttu-id="b25fe-197">Utwórz folder *Protos* w projekcie klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="b25fe-198">Skopiuj plik *Protos\greet.proto* z usługi gRPC Greeter do projektu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="b25fe-199">Edytuj plik projektu *GrpcGreeterClient.csproj:*</span><span class="sxs-lookup"><span data-stu-id="b25fe-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="b25fe-200">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="b25fe-201">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Edytuj plik projektu**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="b25fe-203">Wybierz plik *GrpcGreeterClient.csproj.*</span><span class="sxs-lookup"><span data-stu-id="b25fe-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-204">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="b25fe-205">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Narzędzia** > **edytuj plik**.</span><span class="sxs-lookup"><span data-stu-id="b25fe-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="b25fe-206">Dodaj grupę elementów z elementem, `<Protobuf>` który odwołuje się do pliku *greet.proto:*</span><span class="sxs-lookup"><span data-stu-id="b25fe-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="b25fe-207">Tworzenie klienta greeter</span><span class="sxs-lookup"><span data-stu-id="b25fe-207">Create the Greeter client</span></span>

<span data-ttu-id="b25fe-208">Skompiluj projekt, `GrpcGreeter` aby utworzyć typy w obszarze nazw.</span><span class="sxs-lookup"><span data-stu-id="b25fe-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="b25fe-209">Typy `GrpcGreeter` są generowane automatycznie przez proces kompilacji.</span><span class="sxs-lookup"><span data-stu-id="b25fe-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="b25fe-210">Zaktualizuj plik *Program.cs* klienta gRPC za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="b25fe-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="b25fe-211">*Program.cs* zawiera punkt wejścia i logikę dla klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="b25fe-212">Klient Greeter jest tworzony przez:</span><span class="sxs-lookup"><span data-stu-id="b25fe-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="b25fe-213">Tworzenie wystąpienia `GrpcChannel` zawierającego informacje dotyczące tworzenia połączenia z usługą gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="b25fe-214">Za `GrpcChannel` pomocą do konstruowania klienta Greeter:</span><span class="sxs-lookup"><span data-stu-id="b25fe-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="b25fe-215">Klient Greeter wywołuje metodę asynchroniczne. `SayHello`</span><span class="sxs-lookup"><span data-stu-id="b25fe-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="b25fe-216">Wynik `SayHello` połączenia jest wyświetlany:</span><span class="sxs-lookup"><span data-stu-id="b25fe-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="b25fe-217">Przetestuj klienta gRPC za pomocą usługi gRPC Greeter</span><span class="sxs-lookup"><span data-stu-id="b25fe-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b25fe-218">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b25fe-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b25fe-219">W usłudze Greeter `Ctrl+F5` naciśnij, aby uruchomić serwer bez debugera.</span><span class="sxs-lookup"><span data-stu-id="b25fe-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="b25fe-220">W `GrpcGreeterClient` projekcie naciśnij, `Ctrl+F5` aby uruchomić klienta bez debugera.</span><span class="sxs-lookup"><span data-stu-id="b25fe-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b25fe-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b25fe-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b25fe-222">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="b25fe-222">Start the Greeter service.</span></span>
* <span data-ttu-id="b25fe-223">Uruchom klienta.</span><span class="sxs-lookup"><span data-stu-id="b25fe-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b25fe-224">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b25fe-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b25fe-225">Uruchom usługę Greeter.</span><span class="sxs-lookup"><span data-stu-id="b25fe-225">Start the Greeter service.</span></span>
* <span data-ttu-id="b25fe-226">Uruchom klienta.</span><span class="sxs-lookup"><span data-stu-id="b25fe-226">Start the client.</span></span>

---

<span data-ttu-id="b25fe-227">Klient wysyła powitanie do usługi z komunikatem zawierającym jego nazwę *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="b25fe-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="b25fe-228">Usługa wysyła komunikat "Hello GreeterClient" jako odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="b25fe-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="b25fe-229">Odpowiedź "Hello GreeterClient" jest wyświetlana w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="b25fe-230">Usługa gRPC rejestruje szczegóły pomyślnego wywołania w dziennikach zapisanych w wierszu polecenia:</span><span class="sxs-lookup"><span data-stu-id="b25fe-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="b25fe-231">Kod w tym artykule wymaga ASP.NET certyfikatu rozwoju HTTPS Core w celu zabezpieczenia usługi gRPC.</span><span class="sxs-lookup"><span data-stu-id="b25fe-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="b25fe-232">Jeśli klient nie powiedzie się z komunikatem, `The remote certificate is invalid according to the validation procedure.`certyfikat dewelopera nie jest zaufany.</span><span class="sxs-lookup"><span data-stu-id="b25fe-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="b25fe-233">Aby uzyskać instrukcje dotyczące rozwiązania tego problemu, zobacz [Ufanie ASP.NET podstawowy certyfikat rozwoju HTTPS w systemach Windows i macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="b25fe-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="b25fe-234">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b25fe-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
