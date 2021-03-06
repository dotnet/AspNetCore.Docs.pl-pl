---
title: Narzędzia dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się więcej o narzędziach dostępnych do kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 19270bb74326dccfee9466b7c1fa61daeab805a2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394463"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="1d334-103">Narzędzia dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1d334-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="1d334-104">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="1d334-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="1d334-105">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="1d334-105">Create a new project.</span></span>

1. <span data-ttu-id="1d334-106">Wybierz pozycję **Blazor aplikacja**.</span><span class="sxs-lookup"><span data-stu-id="1d334-106">Select **Blazor App**.</span></span> <span data-ttu-id="1d334-107">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1d334-107">Select **Next**.</span></span>

1. <span data-ttu-id="1d334-108">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="1d334-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1d334-109">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1d334-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1d334-110">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1d334-110">Select **Create**.</span></span>

1. <span data-ttu-id="1d334-111">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1d334-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1d334-112">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1d334-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1d334-113">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1d334-113">Select **Create**.</span></span>

   <span data-ttu-id="1d334-114">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1d334-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="1d334-115">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1d334-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1d334-116">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="1d334-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="1d334-117">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="1d334-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="1d334-118">Podczas wykonywania hostowanej Blazor WebAssembly aplikacji Uruchom aplikację z **`Server`** projektu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1d334-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="1d334-119">Zainstaluj najnowszą wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1d334-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="1d334-120">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1d334-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="1d334-121">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="1d334-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="1d334-122">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="1d334-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="1d334-123">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1d334-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="1d334-124">W przypadku środowiska hostowanego Blazor WebAssembly Dodaj opcję hostowaną ( `-ho` lub `--hosted` ) do polecenia:</span><span class="sxs-lookup"><span data-stu-id="1d334-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="1d334-125">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1d334-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="1d334-126">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1d334-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1d334-127">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1d334-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="1d334-128">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="1d334-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="1d334-129">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="1d334-129">Select **Yes**.</span></span>

   <span data-ttu-id="1d334-130">**Hostowana Blazor WebAssembly Konfiguracja uruchamiania i zadania**</span><span class="sxs-lookup"><span data-stu-id="1d334-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="1d334-131">W przypadku Blazor WebAssembly rozwiązań hostowanych Dodaj (lub Przenieś) `.vscode` folder z `launch.json` `tasks.json` plikami i do folderu nadrzędnego rozwiązania, który jest folderem zawierającym typowe nazwy folderów projektu `Client` , `Server` i `Shared` .</span><span class="sxs-lookup"><span data-stu-id="1d334-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="1d334-132">Zaktualizuj lub potwierdź, że konfiguracja w `launch.json` plikach i `tasks.json` wykonuje hostowaną Blazor WebAssembly aplikację z **`Server`** projektu.</span><span class="sxs-lookup"><span data-stu-id="1d334-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="1d334-133">**`.vscode/launch.json`** ( `launch` Konfiguracja):</span><span class="sxs-lookup"><span data-stu-id="1d334-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="1d334-134">W poprzedniej konfiguracji dla bieżącego katalogu roboczego ( `cwd` ) `{SERVER APP FOLDER}` symbol zastępczy jest **`Server`** folderem projektu, zwykle " `Server` ".</span><span class="sxs-lookup"><span data-stu-id="1d334-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="1d334-135">Jeśli zostanie użyta przeglądarka Microsoft Edge i Google Chrome nie jest zainstalowana w systemie, Dodaj dodatkową właściwość `"browser": "edge"` do konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="1d334-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="1d334-136">Przykładem dla folderu projektu `Server` i, który duplikuje program Microsoft Edge jako przeglądarkę dla przebiegów debugowania, zamiast domyślnej przeglądarki Google Chrome:</span><span class="sxs-lookup"><span data-stu-id="1d334-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="1d334-137">**`.vscode/tasks.json`**(argumenty [ `dotnet` polecenia](/dotnet/core/tools/dotnet) ):</span><span class="sxs-lookup"><span data-stu-id="1d334-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="1d334-138">W poprzednim argumencie:</span><span class="sxs-lookup"><span data-stu-id="1d334-138">In the preceding argument:</span></span>

   * <span data-ttu-id="1d334-139">`{SERVER APP FOLDER}`Symbol zastępczy jest **`Server`** folderem projektu, zwykle " `Server` ".</span><span class="sxs-lookup"><span data-stu-id="1d334-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="1d334-140">`{PROJECT NAME}`Symbol zastępczy jest nazwą aplikacji, na przykład na podstawie nazwy rozwiązania, a po niej " `.Server` " w aplikacji wygenerowanej na podstawie [ Blazor szablonu projektu](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="1d334-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the [Blazor project template](xref:blazor/project-structure).</span></span>

   <span data-ttu-id="1d334-141">Poniższy przykład z [samouczka dotyczącego używania SignalR z Blazor WebAssembly aplikacją](xref:tutorials/signalr-blazor) używa nazwy folderu projektu `Server` i nazwy projektu `BlazorWebAssemblySignalRApp.Server` :</span><span class="sxs-lookup"><span data-stu-id="1d334-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="1d334-142">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="1d334-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="1d334-143">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="1d334-143">Trust a development certificate</span></span>

<span data-ttu-id="1d334-144">Nie istnieje scentralizowany sposób zaufania certyfikatu w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="1d334-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="1d334-145">Zazwyczaj przyjmuje się jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1d334-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="1d334-146">Wyklucz adres URL aplikacji na liście wykluczeń przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1d334-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="1d334-147">Ufaj wszystkim certyfikatom z podpisem własnym dla programu `localhost` .</span><span class="sxs-lookup"><span data-stu-id="1d334-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="1d334-148">Dodaj certyfikat do listy zaufanych certyfikatów w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1d334-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="1d334-149">Aby uzyskać więcej informacji, zobacz wskazówki dostarczone przez producenta przeglądarki i dystrybucję systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="1d334-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="1d334-150">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1d334-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="1d334-151">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="1d334-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1d334-152">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="1d334-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="1d334-153">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1d334-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1d334-154">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1d334-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1d334-155">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1d334-155">Select **Next**.</span></span>

   <span data-ttu-id="1d334-156">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1d334-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1d334-157">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="1d334-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="1d334-158">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1d334-158">Select **Next**.</span></span>

1. <span data-ttu-id="1d334-159">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1d334-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="1d334-160">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="1d334-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="1d334-161">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1d334-161">Select **Create**.</span></span>

1. <span data-ttu-id="1d334-162">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="1d334-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="1d334-163">Uruchom **aplikację z**  >  przyciskiem Uruchom **debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="1d334-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="1d334-164">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="1d334-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1d334-165">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1d334-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="1d334-166">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="1d334-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="1d334-167">Podczas wykonywania hostowanej Blazor WebAssembly aplikacji Uruchom aplikację z **`Server`** projektu rozwiązania.</span><span class="sxs-lookup"><span data-stu-id="1d334-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="1d334-168">Używanie Visual Studio Code do tworzenia aplikacji dla wielu platform Blazor</span><span class="sxs-lookup"><span data-stu-id="1d334-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="1d334-169">[Visual Studio Code](https://code.visualstudio.com/) to środowisko programistyczne (IDE) platformy Open Source, które może służyć do tworzenia Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1d334-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="1d334-170">Użyj interfejsu wiersza polecenia platformy .NET, aby utworzyć nową Blazor aplikację do tworzenia przy użyciu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1d334-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="1d334-171">Aby uzyskać więcej informacji, zobacz [wersję systemu Linux w tym artykule](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="1d334-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="1d334-172">Blazor Opcje szablonu</span><span class="sxs-lookup"><span data-stu-id="1d334-172">Blazor template options</span></span>

<span data-ttu-id="1d334-173">BlazorStruktura zawiera szablony służące do tworzenia nowych aplikacji dla każdego z dwóch Blazor modeli hostingu.</span><span class="sxs-lookup"><span data-stu-id="1d334-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="1d334-174">Szablony są używane do tworzenia nowych Blazor projektów i rozwiązań, niezależnie od narzędzi wybranych do Blazor programowania (Visual Studio, Visual Studio dla komputerów Mac, Visual Studio Code lub interfejsu wiersza polecenia platformy .NET):</span><span class="sxs-lookup"><span data-stu-id="1d334-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="1d334-175">Blazor WebAssembly szablon projektu: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="1d334-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="1d334-176">Blazor Server szablon projektu: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="1d334-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="1d334-177">Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1d334-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="1d334-178">Aby uzyskać więcej informacji na temat Blazor szablonów projektów, zobacz <xref:blazor/project-structure> .</span><span class="sxs-lookup"><span data-stu-id="1d334-178">For more information on Blazor project templates, see <xref:blazor/project-structure>.</span></span>

<span data-ttu-id="1d334-179">Opcje szablonu są dostępne przez przekazanie opcji pomocy ( `-h` lub `--help` ) do [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia CLI w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1d334-179">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```

## <a name="additional-resources"></a><span data-ttu-id="1d334-180">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="1d334-180">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/project-structure>
