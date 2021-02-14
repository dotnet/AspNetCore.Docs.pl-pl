---
title: Narzędzia dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się więcej o narzędziach dostępnych do kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: a17b16563ac12d634e6bdc32638991f45e2a66d5
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280679"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="1b36e-103">Narzędzia dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1b36e-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="1b36e-104">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="1b36e-105">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-105">Create a new project.</span></span>

1. <span data-ttu-id="1b36e-106">Wybierz pozycję **Blazor aplikacja**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-106">Select **Blazor App**.</span></span> <span data-ttu-id="1b36e-107">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-107">Select **Next**.</span></span>

1. <span data-ttu-id="1b36e-108">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1b36e-109">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1b36e-110">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-110">Select **Create**.</span></span>

1. <span data-ttu-id="1b36e-111">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1b36e-112">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1b36e-113">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-113">Select **Create**.</span></span>

   <span data-ttu-id="1b36e-114">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="1b36e-115">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1b36e-116">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="1b36e-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="1b36e-117">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="1b36e-118">Zainstaluj najnowszą wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="1b36e-118">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="1b36e-119">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b36e-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="1b36e-120">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="1b36e-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="1b36e-121">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="1b36e-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="1b36e-122">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b36e-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="1b36e-123">W przypadku środowiska hostowanego Blazor WebAssembly Dodaj opcję hostowaną ( `-ho` lub `--hosted` ) do polecenia:</span><span class="sxs-lookup"><span data-stu-id="1b36e-123">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="1b36e-124">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b36e-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="1b36e-125">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-125">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1b36e-126">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1b36e-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="1b36e-127">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="1b36e-128">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-128">Select **Yes**.</span></span>

1. <span data-ttu-id="1b36e-129">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="1b36e-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="1b36e-130">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="1b36e-130">Trust a development certificate</span></span>

<span data-ttu-id="1b36e-131">Nie istnieje scentralizowany sposób zaufania certyfikatu w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="1b36e-131">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="1b36e-132">Zazwyczaj przyjmuje się jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1b36e-132">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="1b36e-133">Wyklucz adres URL aplikacji na liście wykluczeń przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="1b36e-133">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="1b36e-134">Ufaj wszystkim certyfikatom z podpisem własnym dla programu `localhost` .</span><span class="sxs-lookup"><span data-stu-id="1b36e-134">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="1b36e-135">Dodaj certyfikat do listy zaufanych certyfikatów w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1b36e-135">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="1b36e-136">Aby uzyskać więcej informacji, zobacz wskazówki dostarczone przez producenta przeglądarki i dystrybucję systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="1b36e-136">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="1b36e-137">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1b36e-137">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="1b36e-138">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-138">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="1b36e-139">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="1b36e-139">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="1b36e-140">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-140">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1b36e-141">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-141">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1b36e-142">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-142">Select **Next**.</span></span>

   <span data-ttu-id="1b36e-143">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-143">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="1b36e-144">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="1b36e-145">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-145">Select **Next**.</span></span>

1. <span data-ttu-id="1b36e-146">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="1b36e-146">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="1b36e-147">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="1b36e-147">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="1b36e-148">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="1b36e-148">Select **Create**.</span></span>

1. <span data-ttu-id="1b36e-149">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="1b36e-149">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="1b36e-150">Uruchom **aplikację z**  >  przyciskiem Uruchom **debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="1b36e-150">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="1b36e-151">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="1b36e-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="1b36e-152">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-152">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="1b36e-153">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-153">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="1b36e-154">Używanie Visual Studio Code do tworzenia aplikacji dla wielu platform Blazor</span><span class="sxs-lookup"><span data-stu-id="1b36e-154">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="1b36e-155">[Visual Studio Code](https://code.visualstudio.com/) to środowisko programistyczne (IDE) platformy Open Source, które może służyć do tworzenia Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b36e-155">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="1b36e-156">Użyj interfejsu wiersza polecenia platformy .NET, aby utworzyć nową Blazor aplikację do tworzenia przy użyciu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1b36e-156">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="1b36e-157">Aby uzyskać więcej informacji, zobacz [wersję systemu Linux w tym artykule](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="1b36e-157">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="1b36e-158">Blazor Opcje szablonu</span><span class="sxs-lookup"><span data-stu-id="1b36e-158">Blazor template options</span></span>

<span data-ttu-id="1b36e-159">BlazorStruktura zawiera szablony służące do tworzenia nowych aplikacji dla każdego z dwóch Blazor modeli hostingu.</span><span class="sxs-lookup"><span data-stu-id="1b36e-159">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="1b36e-160">Szablony są używane do tworzenia nowych Blazor projektów i rozwiązań, niezależnie od narzędzi wybranych do Blazor programowania (Visual Studio, Visual Studio dla komputerów Mac, Visual Studio Code lub interfejsu wiersza polecenia platformy .NET):</span><span class="sxs-lookup"><span data-stu-id="1b36e-160">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="1b36e-161">Blazor WebAssembly szablon projektu: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="1b36e-161">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="1b36e-162">Blazor Server szablon projektu: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="1b36e-162">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="1b36e-163">Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="1b36e-163">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="1b36e-164">Opcje szablonu są dostępne przez przekazanie opcji pomocy ( `-h` lub `--help` ) do [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia CLI w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b36e-164">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```