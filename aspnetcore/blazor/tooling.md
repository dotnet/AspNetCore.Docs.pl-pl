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
ms.openlocfilehash: 29f1a1f211688a1edcd31c7230e7216df7c89eef
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506815"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="baf2d-103">Narzędzia dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="baf2d-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="baf2d-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="baf2d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="baf2d-105">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="baf2d-106">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-106">Create a new project.</span></span>

1. <span data-ttu-id="baf2d-107">Wybierz pozycję **Blazor aplikacja**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-107">Select **Blazor App**.</span></span> <span data-ttu-id="baf2d-108">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-108">Select **Next**.</span></span>

1. <span data-ttu-id="baf2d-109">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="baf2d-110">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="baf2d-111">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-111">Select **Create**.</span></span>

1. <span data-ttu-id="baf2d-112">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="baf2d-113">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="baf2d-114">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-114">Select **Create**.</span></span>

   <span data-ttu-id="baf2d-115">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="baf2d-116">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="baf2d-117">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="baf2d-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="baf2d-118">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="baf2d-119">Zainstaluj najnowszą wersję [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="baf2d-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="baf2d-120">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="baf2d-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="baf2d-121">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="baf2d-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="baf2d-122">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="baf2d-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="baf2d-123">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="baf2d-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="baf2d-124">W przypadku środowiska hostowanego Blazor WebAssembly Dodaj opcję hostowaną ( `-ho` lub `--hosted` ) do polecenia:</span><span class="sxs-lookup"><span data-stu-id="baf2d-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="baf2d-125">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="baf2d-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="baf2d-126">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="baf2d-127">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="baf2d-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="baf2d-128">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="baf2d-129">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-129">Select **Yes**.</span></span>

1. <span data-ttu-id="baf2d-130">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="baf2d-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="baf2d-131">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="baf2d-131">Trust a development certificate</span></span>

<span data-ttu-id="baf2d-132">Nie istnieje scentralizowany sposób zaufania certyfikatu w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="baf2d-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="baf2d-133">Zazwyczaj przyjmuje się jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="baf2d-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="baf2d-134">Wyklucz adres URL aplikacji na liście wykluczeń przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="baf2d-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="baf2d-135">Ufaj wszystkim certyfikatom z podpisem własnym dla programu `localhost` .</span><span class="sxs-lookup"><span data-stu-id="baf2d-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="baf2d-136">Dodaj certyfikat do listy zaufanych certyfikatów w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="baf2d-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="baf2d-137">Aby uzyskać więcej informacji, zobacz wskazówki dostarczone przez producenta przeglądarki i dystrybucję systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="baf2d-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="baf2d-138">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="baf2d-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="baf2d-139">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="baf2d-140">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  .</span><span class="sxs-lookup"><span data-stu-id="baf2d-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="baf2d-141">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon **Blazor WebAssembly aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="baf2d-142">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon **Blazor Server aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="baf2d-143">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-143">Select **Next**.</span></span>

   <span data-ttu-id="baf2d-144">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* (autonomicznych i hostowanych) i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="baf2d-145">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="baf2d-146">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-146">Select **Next**.</span></span>

1. <span data-ttu-id="baf2d-147">W przypadku środowiska hostowanego Blazor WebAssembly zaznacz pole wyboru **hostowane ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="baf2d-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="baf2d-148">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="baf2d-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="baf2d-149">Wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="baf2d-149">Select **Create**.</span></span>

1. <span data-ttu-id="baf2d-150">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="baf2d-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="baf2d-151">Uruchom **aplikację z**  >  przyciskiem Uruchom **debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="baf2d-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="baf2d-152">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="baf2d-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="baf2d-153">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="baf2d-154">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="baf2d-155">Używanie Visual Studio Code do tworzenia aplikacji dla wielu platform Blazor</span><span class="sxs-lookup"><span data-stu-id="baf2d-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="baf2d-156">[Visual Studio Code](https://code.visualstudio.com/) to środowisko programistyczne (IDE) platformy Open Source, które może służyć do tworzenia Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="baf2d-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="baf2d-157">Użyj interfejsu wiersza polecenia platformy .NET, aby utworzyć nową Blazor aplikację do tworzenia przy użyciu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="baf2d-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="baf2d-158">Aby uzyskać więcej informacji, zobacz [wersję systemu Linux w tym artykule](/aspnet/core/blazor/tooling?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="baf2d-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="baf2d-159">Blazor Opcje szablonu</span><span class="sxs-lookup"><span data-stu-id="baf2d-159">Blazor template options</span></span>

<span data-ttu-id="baf2d-160">BlazorStruktura zawiera szablony służące do tworzenia nowych aplikacji dla każdego z dwóch Blazor modeli hostingu.</span><span class="sxs-lookup"><span data-stu-id="baf2d-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="baf2d-161">Szablony są używane do tworzenia nowych Blazor projektów i rozwiązań, niezależnie od narzędzi wybranych do Blazor programowania (Visual Studio, Visual Studio dla komputerów Mac, Visual Studio Code lub interfejsu wiersza polecenia platformy .NET):</span><span class="sxs-lookup"><span data-stu-id="baf2d-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="baf2d-162">Blazor WebAssembly szablon projektu: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="baf2d-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="baf2d-163">Blazor Server szablon projektu: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="baf2d-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="baf2d-164">Aby uzyskać więcej informacji na temat Blazor modeli hostingu, zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="baf2d-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="baf2d-165">Opcje szablonu są dostępne przez przekazanie opcji pomocy ( `-h` lub `--help` ) do [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia CLI w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="baf2d-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm --h
dotnet new blazorserver --h
```
