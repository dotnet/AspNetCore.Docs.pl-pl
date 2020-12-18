---
title: Wprowadzenie do ASP.NET Core Blazor
author: guardrex
description: Eksploruj ASP.NET Core Blazor , jak tworzyć interaktywny interfejs użytkownika sieci Web po stronie klienta przy użyciu platformy .NET w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 79c225a0714562a01afe67bf8e59f3b3f98a6265
ms.sourcegitcommit: e9b8835a02f75b6378b766edb8bab23b14a4192b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97666862"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="aed9f-103">Wprowadzenie do ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="aed9f-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="aed9f-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aed9f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aed9f-105">*Witamy Blazor !*</span><span class="sxs-lookup"><span data-stu-id="aed9f-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="aed9f-106">Blazor to platforma służąca do tworzenia interakcyjnego interfejsu użytkownika sieci Web po stronie klienta przy użyciu [platformy .NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="aed9f-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="aed9f-107">Twórz rozbudowane interaktywne interfejsów użytkownika przy użyciu [języka C#](/dotnet/csharp/) zamiast [języka JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="aed9f-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="aed9f-108">Udostępnianie logiki aplikacji po stronie serwera i klienta zapisaną w środowisku .NET.</span><span class="sxs-lookup"><span data-stu-id="aed9f-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="aed9f-109">Renderuj interfejs użytkownika jako HTML i CSS, aby obsługiwał szeroką przeglądarkę, w tym przeglądarki dla urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="aed9f-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="aed9f-110">Integruj z nowoczesnymi platformami hostingu, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="aed9f-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="aed9f-111">Korzystanie z programu .NET do tworzenia aplikacji sieci Web po stronie klienta zapewnia następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="aed9f-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="aed9f-112">Napisz kod w języku C# zamiast języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="aed9f-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="aed9f-113">Skorzystaj z istniejącego ekosystemu .NET [bibliotek platformy .NET](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="aed9f-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="aed9f-114">Udostępnianie logiki aplikacji między serwerem i klientem.</span><span class="sxs-lookup"><span data-stu-id="aed9f-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="aed9f-115">Skorzystaj z programu. Wydajność, niezawodność i bezpieczeństwo sieci.</span><span class="sxs-lookup"><span data-stu-id="aed9f-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="aed9f-116">Bądź na bieżąco z programem [Visual Studio](https://visualstudio.microsoft.com) w systemach Windows, Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="aed9f-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="aed9f-117">Twórz w oparciu o wspólny zestaw języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.</span><span class="sxs-lookup"><span data-stu-id="aed9f-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="aed9f-118">Składniki</span><span class="sxs-lookup"><span data-stu-id="aed9f-118">Components</span></span>

<span data-ttu-id="aed9f-119">Blazor aplikacje są oparte na *składnikach*.</span><span class="sxs-lookup"><span data-stu-id="aed9f-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="aed9f-120">Składnik w programie Blazor to element interfejsu użytkownika, taki jak strona, okno dialogowe lub formularz wprowadzania danych.</span><span class="sxs-lookup"><span data-stu-id="aed9f-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="aed9f-121">Składniki to klasy .NET C# wbudowane w [zestawy .NET](/dotnet/standard/assembly/) , które:</span><span class="sxs-lookup"><span data-stu-id="aed9f-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="aed9f-122">Definiowanie elastycznej logiki renderowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="aed9f-123">Obsługa zdarzeń użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-123">Handle user events.</span></span>
* <span data-ttu-id="aed9f-124">Mogą być zagnieżdżane i ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="aed9f-124">Can be nested and reused.</span></span>
* <span data-ttu-id="aed9f-125">Mogą być udostępniane i dystrybuowane jako [ Razor biblioteki klas](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="aed9f-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="aed9f-126">Klasa składnika jest zwykle zapisywana w formie [Razor](xref:mvc/views/razor) strony znaczników z `.razor` rozszerzeniem pliku.</span><span class="sxs-lookup"><span data-stu-id="aed9f-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="aed9f-127">Składniki w programie Blazor są formalnie określane jako *Razor składniki*.</span><span class="sxs-lookup"><span data-stu-id="aed9f-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="aed9f-128">Razor jest składnią służącą do łączenia znaczników HTML z kodem C# zaprojektowanym pod kątem produktywności dla deweloperów.</span><span class="sxs-lookup"><span data-stu-id="aed9f-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="aed9f-129">Razor umożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą programowania [IntelliSense](/visualstudio/ide/using-intellisense) w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="aed9f-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="aed9f-130">Razor Używane są również strony i MVC Razor .</span><span class="sxs-lookup"><span data-stu-id="aed9f-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="aed9f-131">W przeciwieństwie do Razor stron i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="aed9f-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="aed9f-132">Blazor używa naturalnych tagów HTML dla kompozycji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="aed9f-133">Poniższy Razor znacznik ilustruje składnik ( `Dialog.razor` ), który wyświetla okno dialogowe i przetwarza zdarzenie, gdy użytkownik wybierze przycisk:</span><span class="sxs-lookup"><span data-stu-id="aed9f-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="aed9f-134">W poprzednim przykładzie jest to `OnYes` Metoda języka C# wyzwalana przez `onclick` zdarzenie przycisku.</span><span class="sxs-lookup"><span data-stu-id="aed9f-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="aed9f-135">Tekst ( `ChildContent` ) i tytuł () okna dialogowego `Title` są dostarczane przez Poniższy składnik, który używa tego składnika w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="aed9f-136">`Dialog`Składnik jest zagnieżdżony w innym składniku przy użyciu tagu HTML.</span><span class="sxs-lookup"><span data-stu-id="aed9f-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="aed9f-137">W poniższym przykładzie `Index` składnik ( `Pages/Index.razor` ) używa poprzedniego `Dialog` składnika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="aed9f-138">`Title`Atrybut tagu przekazuje wartość tytułu do `Dialog` `Title` właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="aed9f-139">`Dialog`Tekst składnika ( `ChildContent` ) jest ustawiany przez zawartość `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="aed9f-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="aed9f-140">Po `Dialog` dodaniu składnika do składnika funkcja `Index` [IntelliSense w programie Visual Studio](/visualstudio/ide/using-intellisense) przyspiesza Programowanie przy użyciu składni i uzupełniania parametrów.</span><span class="sxs-lookup"><span data-stu-id="aed9f-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="aed9f-141">To okno dialogowe jest renderowane, gdy `Index` dostęp do składnika zostanie uzyskany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="aed9f-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="aed9f-142">Po wybraniu przycisku przez użytkownika konsola narzędzia deweloperskie w przeglądarce pokazuje komunikat zapisany przez `OnYes` metodę:</span><span class="sxs-lookup"><span data-stu-id="aed9f-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Składnik okna dialogowego renderowany w przeglądarce zagnieżdżony wewnątrz składnika indeksu.](index/_static/dialog.png)

<span data-ttu-id="aed9f-146">Składniki są renderowane w pamięci podręcznej [Document Object Model (dom)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) przeglądarki o nazwie *drzewo renderowania*, która jest używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.</span><span class="sxs-lookup"><span data-stu-id="aed9f-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="aed9f-147">Blazor WebAssembly to [Struktura aplikacji jednostronicowej (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) do tworzenia interaktywnych aplikacji sieci Web po stronie klienta przy użyciu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="aed9f-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="aed9f-148">Blazor WebAssembly używa otwartych standardów sieci Web bez wtyczek lub ponownego kompilowania kodu w innych językach.</span><span class="sxs-lookup"><span data-stu-id="aed9f-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="aed9f-149">Blazor WebAssembly działa we wszystkich nowoczesnych przeglądarkach sieci Web, w tym w przeglądarkach dla urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="aed9f-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="aed9f-150">Uruchamianie kodu platformy .NET wewnątrz przeglądarek sieci Web jest możliwe przez [zestaw webassembly](https://webassembly.org) (skrócony `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="aed9f-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="aed9f-151">Webassembly to kompaktowy format kodu bajtowego zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania.</span><span class="sxs-lookup"><span data-stu-id="aed9f-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="aed9f-152">Webassembly to otwarty standard sieci Web, który jest obsługiwany w przeglądarkach sieci Web bez wtyczek.</span><span class="sxs-lookup"><span data-stu-id="aed9f-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="aed9f-153">Kod webassembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem języka JavaScript, nazywanego *współdziałaniem języka JavaScript*, często skracana do międzyoperacyjności *JavaScript Interop* lub *js*.</span><span class="sxs-lookup"><span data-stu-id="aed9f-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="aed9f-154">Kod .NET wykonywany za pośrednictwem webassembly w przeglądarce jest uruchamiany w piaskownicy języka JavaScript przeglądarki z ochroną, którą piaskownica zapewnia przed złośliwymi działaniami na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="aed9f-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![::: No-Loc (Blazor webassembly)::: uruchamia kod .NET w przeglądarce z zestawem webassembly.](index/_static/blazor-webassembly.png)

<span data-ttu-id="aed9f-156">Gdy Blazor WebAssembly aplikacja zostanie skompilowana i uruchomiona w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="aed9f-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="aed9f-157">Pliki kodu C# i Razor pliki są kompilowane do zestawów .NET.</span><span class="sxs-lookup"><span data-stu-id="aed9f-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="aed9f-158">Zestawy i [środowisko uruchomieniowe platformy .NET](/dotnet/framework/get-started/overview) są pobierane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="aed9f-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="aed9f-159">Blazor WebAssembly ładuje uruchomienia środowiska uruchomieniowego .NET i konfiguruje środowisko uruchomieniowe w celu załadowania zestawów dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed9f-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="aed9f-160">Blazor WebAssemblyŚrodowisko uruchomieniowe używa międzyoperacyjnego języka JavaScript do obsługi operacji manipulowania Dom i interfejsu API przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="aed9f-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="aed9f-161">Rozmiar opublikowanej aplikacji, jej *rozmiaru ładunku*, stanowi krytyczny współczynnik wydajności dla użyteczności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="aed9f-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="aed9f-162">Pobieranie dużej aplikacji do przeglądarki zajmuje stosunkowo dużo czasu, co zmniejsza środowisko użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="aed9f-163">Blazor WebAssembly optymalizuje rozmiar ładunku, aby skrócić czas pobierania:</span><span class="sxs-lookup"><span data-stu-id="aed9f-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="aed9f-164">Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany za pomocą elementu [dostosowującego języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-trimmer).</span><span class="sxs-lookup"><span data-stu-id="aed9f-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="aed9f-165">Odpowiedzi HTTP są kompresowane.</span><span class="sxs-lookup"><span data-stu-id="aed9f-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="aed9f-166">Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="aed9f-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="aed9f-167">Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [konsolidator języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="aed9f-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="aed9f-168">Odpowiedzi HTTP są kompresowane.</span><span class="sxs-lookup"><span data-stu-id="aed9f-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="aed9f-169">Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="aed9f-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="aed9f-170">Blazor oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="aed9f-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="aed9f-171">*Blazor Server* zapewnia obsługę składników hostingu Razor na serwerze w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aed9f-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="aed9f-172">Aktualizacje interfejsu użytkownika są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="aed9f-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="aed9f-173">Obsługa środowiska uruchomieniowego:</span><span class="sxs-lookup"><span data-stu-id="aed9f-173">The runtime handles:</span></span>

* <span data-ttu-id="aed9f-174">Wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera programu.</span><span class="sxs-lookup"><span data-stu-id="aed9f-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="aed9f-175">Stosowanie aktualizacji interfejsu użytkownika do renderowanego składnika, który jest wysyłany z powrotem przez serwer.</span><span class="sxs-lookup"><span data-stu-id="aed9f-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="aed9f-176">Połączenie używane przez Blazor Server program do komunikacji z przeglądarką jest również używane do obsługi wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="aed9f-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![::: No-Loc (Blazor Server)::: uruchamia kod platformy .NET na serwerze i współdziała z Document Object Model na kliencie za pośrednictwem::: No-Loc (Sygnalizującer)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="aed9f-178">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="aed9f-178">JavaScript interop</span></span>

<span data-ttu-id="aed9f-179">W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z JavaScript.</span><span class="sxs-lookup"><span data-stu-id="aed9f-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="aed9f-180">Składniki mogą korzystać z dowolnej biblioteki lub interfejsu API, który może być używany przez język JavaScript.</span><span class="sxs-lookup"><span data-stu-id="aed9f-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="aed9f-181">Kod c# może [wywołać kod JavaScript](xref:blazor/call-javascript-from-dotnet), a kod JavaScript może [wywołać kod C#](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="aed9f-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="aed9f-182">Udostępnianie kodu i .NET Standard</span><span class="sxs-lookup"><span data-stu-id="aed9f-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="aed9f-183">Blazor implementuje [.NET Standard](/dotnet/standard/net-standard), dzięki czemu Blazor projekty mogą odwoływać się do bibliotek, które są zgodne ze specyfikacjami .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="aed9f-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="aed9f-184">.NET Standard jest formalną specyfikacją interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="aed9f-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="aed9f-185">Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor .NET Framework, .NET Core, Xamarin, mono i Unity.</span><span class="sxs-lookup"><span data-stu-id="aed9f-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="aed9f-186">Interfejsy API, które nie są stosowane w przeglądarce sieci Web (na przykład dostęp do systemu plików, otwieranie gniazda i wątkowość) throw <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="aed9f-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aed9f-187">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="aed9f-187">Additional resources</span></span>

* [<span data-ttu-id="aed9f-188">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="aed9f-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="aed9f-189">Przewodnik języka C#</span><span class="sxs-lookup"><span data-stu-id="aed9f-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="aed9f-190">HTML</span><span class="sxs-lookup"><span data-stu-id="aed9f-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="aed9f-191">[Firma Blazor Awesome](https://github.com/AdrienTorris/awesome-blazor) linki społeczności</span><span class="sxs-lookup"><span data-stu-id="aed9f-191">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
