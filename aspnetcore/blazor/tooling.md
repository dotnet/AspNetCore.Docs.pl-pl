---
title: Narzędzia dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się więcej o narzędziach dostępnych do kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 094ec24fb6599168f0aa84f16c00deabf5f239b7
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913835"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="3aedb-103">Narzędzia dla ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="3aedb-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3aedb-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3aedb-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="3aedb-105">Zainstaluj najnowszą wersję programu [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) z **ASP.NET i programowaniem aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="3aedb-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="3aedb-106">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="3aedb-106">Create a new project.</span></span>

1. <span data-ttu-id="3aedb-107">Wybierz pozycję \*\* Blazor aplikacja\*\*.</span><span class="sxs-lookup"><span data-stu-id="3aedb-107">Select **Blazor App**.</span></span> <span data-ttu-id="3aedb-108">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-108">Select **Next**.</span></span>

1. <span data-ttu-id="3aedb-109">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="3aedb-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3aedb-110">Potwierdź, że wpis **lokalizacji** jest poprawny lub podaj lokalizację dla projektu.</span><span class="sxs-lookup"><span data-stu-id="3aedb-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3aedb-111">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-111">Select **Create**.</span></span>

1. <span data-ttu-id="3aedb-112">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="3aedb-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3aedb-113">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="3aedb-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3aedb-114">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-114">Select **Create**.</span></span>

   <span data-ttu-id="3aedb-115">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3aedb-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3aedb-116">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aedb-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="3aedb-117">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="3aedb-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="3aedb-118">Zainstaluj najnowszą wersję [zestawu SDK programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="3aedb-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="3aedb-119">Jeśli wcześniej zainstalowano zestaw SDK, możesz określić zainstalowaną wersję, wykonując następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3aedb-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="3aedb-120">Zainstaluj najnowszą wersję programu [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="3aedb-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="3aedb-121">Zainstaluj najnowszą wersję [języka C# dla rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="3aedb-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="3aedb-122">Aby uzyskać Blazor WebAssembly środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3aedb-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="3aedb-123">Aby uzyskać Blazor Server środowisko, wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="3aedb-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3aedb-124">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3aedb-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3aedb-125">Otwórz `WebApplication1` folder w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3aedb-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3aedb-126">Żądania IDE służące do dodawania zasobów do kompilowania i debugowania projektu.</span><span class="sxs-lookup"><span data-stu-id="3aedb-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3aedb-127">Wybierz pozycję **Tak**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-127">Select **Yes**.</span></span>

1. <span data-ttu-id="3aedb-128">Naciśnij klawisz <kbd>Ctrl</kbd> + <kbd>F5</kbd> , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aedb-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="3aedb-129">Ufanie certyfikatowi Deweloperskiemu</span><span class="sxs-lookup"><span data-stu-id="3aedb-129">Trust a development certificate</span></span>

<span data-ttu-id="3aedb-130">Nie istnieje scentralizowany sposób zaufania certyfikatu w systemie Linux.</span><span class="sxs-lookup"><span data-stu-id="3aedb-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="3aedb-131">Zazwyczaj przyjmuje się jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="3aedb-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="3aedb-132">Wyklucz adres URL aplikacji na liście wykluczeń przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="3aedb-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="3aedb-133">Ufaj wszystkim certyfikatom z podpisem własnym dla programu `localhost` .</span><span class="sxs-lookup"><span data-stu-id="3aedb-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="3aedb-134">Dodaj certyfikat do listy zaufanych certyfikatów w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="3aedb-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="3aedb-135">Aby uzyskać więcej informacji, zapoznaj się ze wskazówkami podanymi w przeglądarce i dystrybucji systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="3aedb-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="3aedb-136">Zainstaluj [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="3aedb-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3aedb-137">Wybierz pozycję **plik**  >  **nowe rozwiązanie** lub Utwórz **Nowy** projekt z **okna uruchamiania**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3aedb-138">Na pasku bocznym wybierz pozycję Aplikacja **sieci Web i konsola**  >  **App**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="3aedb-139">Aby zapoznać się z Blazor WebAssembly doświadczeniem, wybierz szablon \*\* Blazor WebAssembly aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="3aedb-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3aedb-140">Aby zapoznać się z Blazor Server doświadczeniem, wybierz szablon \*\* Blazor Server aplikacji\*\* .</span><span class="sxs-lookup"><span data-stu-id="3aedb-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3aedb-141">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-141">Select **Next**.</span></span>

   <span data-ttu-id="3aedb-142">Aby uzyskać informacje na temat dwóch Blazor modeli hostingu *Blazor WebAssembly* i *Blazor Server* , zobacz <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="3aedb-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3aedb-143">Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3aedb-144">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-144">Select **Next**.</span></span>

1. <span data-ttu-id="3aedb-145">W polu **Nazwa projektu** Nadaj nazwę aplikacji `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="3aedb-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3aedb-146">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aedb-146">Select **Create**.</span></span>

1. <span data-ttu-id="3aedb-147">Wybierz pozycję **Uruchom**  >  **Uruchom bez debugowania** , aby uruchomić aplikację *bez debugera*.</span><span class="sxs-lookup"><span data-stu-id="3aedb-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3aedb-148">Uruchom **aplikację z**  >  przyciskiem Uruchom**debugowanie** lub Uruchom (&#9654;), aby uruchomić aplikację *z debugerem*.</span><span class="sxs-lookup"><span data-stu-id="3aedb-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="3aedb-149">Jeśli zostanie wyświetlony monit o zaufać certyfikatowi Deweloperskiemu, zaufaj certyfikatowi i Kontynuuj.</span><span class="sxs-lookup"><span data-stu-id="3aedb-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3aedb-150">Hasła użytkownika i pęku kluczy są wymagane do zaufania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="3aedb-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="3aedb-151">Aby uzyskać więcej informacji na temat ufania certyfikatowi Deweloperskiemu protokołu HTTPS ASP.NET Core, zobacz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .</span><span class="sxs-lookup"><span data-stu-id="3aedb-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
