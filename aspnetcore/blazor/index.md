---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Eksploruj ASP.NET Core Blazor , jak tworzyć interaktywny interfejs użytkownika sieci Web po stronie klienta przy użyciu platformy .NET w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 06/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 30f11a137e711b1cf7a8b036af92fbb5fa2a1f05
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402575"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="b81a4-103">Wprowadzenie do ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="b81a4-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="b81a4-104">Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b81a4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b81a4-105">*Witamy Blazor !*</span><span class="sxs-lookup"><span data-stu-id="b81a4-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="b81a4-106">to platforma służąca do tworzenia interakcyjnego interfejsu użytkownika sieci Web po stronie klienta przy użyciu platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="b81a4-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="b81a4-107">Twórz rozbudowane interaktywne interfejsów użytkownika przy użyciu języka C# zamiast języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b81a4-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="b81a4-108">Udostępnianie logiki aplikacji po stronie serwera i klienta zapisaną w środowisku .NET.</span><span class="sxs-lookup"><span data-stu-id="b81a4-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="b81a4-109">Renderuj interfejs użytkownika jako HTML i CSS, aby obsługiwał szeroką przeglądarkę, w tym przeglądarki dla urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="b81a4-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="b81a4-110">Integruj z nowoczesnymi platformami hostingu, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="b81a4-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="b81a4-111">Korzystanie z programu .NET do tworzenia aplikacji sieci Web po stronie klienta zapewnia następujące korzyści:</span><span class="sxs-lookup"><span data-stu-id="b81a4-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="b81a4-112">Napisz kod w języku C# zamiast języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b81a4-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="b81a4-113">Skorzystaj z istniejącego ekosystemu .NET bibliotek platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b81a4-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="b81a4-114">Udostępnianie logiki aplikacji między serwerem i klientem.</span><span class="sxs-lookup"><span data-stu-id="b81a4-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="b81a4-115">Skorzystaj z programu. Wydajność, niezawodność i bezpieczeństwo sieci.</span><span class="sxs-lookup"><span data-stu-id="b81a4-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="b81a4-116">Bądź na bieżąco z programem Visual Studio w systemach Windows, Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="b81a4-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="b81a4-117">Twórz w oparciu o wspólny zestaw języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.</span><span class="sxs-lookup"><span data-stu-id="b81a4-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="b81a4-118">Składniki</span><span class="sxs-lookup"><span data-stu-id="b81a4-118">Components</span></span>

Blazor<span data-ttu-id="b81a4-119">aplikacje są oparte na *składnikach*.</span><span class="sxs-lookup"><span data-stu-id="b81a4-119"> apps are based on *components*.</span></span> <span data-ttu-id="b81a4-120">Składnik w programie Blazor to element interfejsu użytkownika, taki jak strona, okno dialogowe lub formularz wprowadzania danych.</span><span class="sxs-lookup"><span data-stu-id="b81a4-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="b81a4-121">Składniki to klasy .NET wbudowane w zestawy .NET, które:</span><span class="sxs-lookup"><span data-stu-id="b81a4-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="b81a4-122">Definiowanie elastycznej logiki renderowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="b81a4-123">Obsługa zdarzeń użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-123">Handle user events.</span></span>
* <span data-ttu-id="b81a4-124">Mogą być zagnieżdżane i ponownie używane.</span><span class="sxs-lookup"><span data-stu-id="b81a4-124">Can be nested and reused.</span></span>
* <span data-ttu-id="b81a4-125">Mogą być udostępniane i dystrybuowane jako [ Razor biblioteki klas](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="b81a4-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="b81a4-126">Klasa składnika jest zwykle zapisywana w formie [Razor](xref:mvc/views/razor) strony znaczników z `.razor` rozszerzeniem pliku.</span><span class="sxs-lookup"><span data-stu-id="b81a4-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="b81a4-127">Składniki w programie Blazor są formalnie określane jako \* Razor składniki\*.</span><span class="sxs-lookup"><span data-stu-id="b81a4-127">Components in Blazor are formally referred to as *Razor components*.</span></span> Razor<span data-ttu-id="b81a4-128">jest składnią służącą do łączenia znaczników HTML z kodem C# zaprojektowanym pod kątem produktywności dla deweloperów.</span><span class="sxs-lookup"><span data-stu-id="b81a4-128"> is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> Razor<span data-ttu-id="b81a4-129">umożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą [IntelliSense](/visualstudio/ide/using-intellisense) .</span><span class="sxs-lookup"><span data-stu-id="b81a4-129"> allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> Razor<span data-ttu-id="b81a4-130">Używane są również strony i MVC Razor .</span><span class="sxs-lookup"><span data-stu-id="b81a4-130"> Pages and MVC also use Razor.</span></span> <span data-ttu-id="b81a4-131">W przeciwieństwie do Razor stron i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="b81a4-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="b81a4-132">Poniższy Razor znacznik ilustruje składnik ( `Dialog.razor` ), który może być zagnieżdżony w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="b81a4-132">The following Razor markup demonstrates a component (`Dialog.razor`), which can be nested within another component:</span></span>

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

<span data-ttu-id="b81a4-133">Zawartość ( `ChildContent` ) i tytuł () okna dialogowego `Title` są udostępniane przez składnik, który używa tego składnika w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="b81a4-134">`OnYes`jest metodą języka C# wyzwalaną przez zdarzenie przycisku `onclick` .</span><span class="sxs-lookup"><span data-stu-id="b81a4-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="b81a4-135">używa naturalnych tagów HTML dla kompozycji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="b81a4-136">Elementy HTML określają składniki, a atrybuty znacznika przechodzą wartości do właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="b81a4-137">W poniższym przykładzie `Index` składnik używa `Dialog` składnika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="b81a4-138">`ChildContent`i `Title` są ustawiane przez atrybuty i zawartość `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="b81a4-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="b81a4-139">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="b81a4-139">`Pages/Index.razor`:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="b81a4-140">Okno dialogowe jest renderowane po uzyskaniu dostępu do elementu nadrzędnego ( `Pages/Index.razor` ) w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="b81a4-140">The dialog is rendered when the parent (`Pages/Index.razor`) is accessed in a browser:</span></span>

![Składnik okna dialogowego renderowany w przeglądarce](index/_static/dialog.png)

<span data-ttu-id="b81a4-142">Gdy ten składnik jest używany w aplikacji, funkcja IntelliSense w programie [Visual Studio](/visualstudio/ide/using-intellisense) i [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) przyspiesza Programowanie przy użyciu składni i uzupełniania parametrów.</span><span class="sxs-lookup"><span data-stu-id="b81a4-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="b81a4-143">Składniki są renderowane w pamięci podręcznej Document Object Model (DOM) przeglądarki o nazwie *drzewo renderowania*, która jest używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.</span><span class="sxs-lookup"><span data-stu-id="b81a4-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

Blazor WebAssembly<span data-ttu-id="b81a4-144">jest platformą aplikacji jednostronicowej do tworzenia interaktywnych aplikacji sieci Web po stronie klienta przy użyciu platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b81a4-144"> is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor WebAssembly<span data-ttu-id="b81a4-145">używa otwartych standardów sieci Web bez wtyczek lub kodu transpilation i działa we wszystkich nowoczesnych przeglądarkach sieci Web, w tym w przeglądarkach mobilnych.</span><span class="sxs-lookup"><span data-stu-id="b81a4-145"> uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="b81a4-146">Uruchamianie kodu platformy .NET wewnątrz przeglądarek sieci Web jest możliwe przez [zestaw webassembly](https://webassembly.org) (skrócony `wasm` ).</span><span class="sxs-lookup"><span data-stu-id="b81a4-146">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="b81a4-147">Webassembly to kompaktowy format kodu bajtowego zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania.</span><span class="sxs-lookup"><span data-stu-id="b81a4-147">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="b81a4-148">Webassembly to otwarty standard sieci Web, który jest obsługiwany w przeglądarkach sieci Web bez wtyczek.</span><span class="sxs-lookup"><span data-stu-id="b81a4-148">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="b81a4-149">Kod webassembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem języka JavaScript, nazywanego *współdziałaniem JavaScript* (lub *międzyoperacyjną JavaScript*).</span><span class="sxs-lookup"><span data-stu-id="b81a4-149">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="b81a4-150">Kod .NET wykonywany za pośrednictwem webassembly w przeglądarce jest uruchamiany w piaskownicy języka JavaScript przeglądarki z ochroną, którą piaskownica zapewnia przed złośliwymi działaniami na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="b81a4-150">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="b81a4-151">![Blazor WebAssemblyuruchamia kod platformy .NET w przeglądarce z zestawem webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="b81a4-151">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="b81a4-152">Gdy Blazor WebAssembly aplikacja zostanie skompilowana i uruchomiona w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="b81a4-152">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="b81a4-153">Pliki kodu C# i Razor pliki są kompilowane do zestawów .NET.</span><span class="sxs-lookup"><span data-stu-id="b81a4-153">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="b81a4-154">Zestawy i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b81a4-154">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor WebAssembly<span data-ttu-id="b81a4-155">ładuje uruchomienia środowiska uruchomieniowego .NET i konfiguruje środowisko uruchomieniowe w celu załadowania zestawów dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b81a4-155"> bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="b81a4-156">Blazor WebAssemblyŚrodowisko uruchomieniowe używa międzyoperacyjnego języka JavaScript do obsługi operacji manipulowania Dom i interfejsu API przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b81a4-156">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="b81a4-157">Rozmiar opublikowanej aplikacji, jej *rozmiaru ładunku*, jest krytycznym czynnikiem wydajności dla useability aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b81a4-157">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="b81a4-158">Pobieranie dużej aplikacji do przeglądarki zajmuje stosunkowo dużo czasu, co zmniejsza środowisko użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-158">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor WebAssembly<span data-ttu-id="b81a4-159">optymalizuje rozmiar ładunku, aby skrócić czas pobierania:</span><span class="sxs-lookup"><span data-stu-id="b81a4-159"> optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="b81a4-160">Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [konsolidator języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-linker).</span><span class="sxs-lookup"><span data-stu-id="b81a4-160">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="b81a4-161">Odpowiedzi HTTP są kompresowane.</span><span class="sxs-lookup"><span data-stu-id="b81a4-161">HTTP responses are compressed.</span></span>
* <span data-ttu-id="b81a4-162">Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="b81a4-162">The .NET runtime and assemblies are cached in the browser.</span></span>

## Blazor Server

Blazor<span data-ttu-id="b81a4-163">oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b81a4-163"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor Server<span data-ttu-id="b81a4-164">zapewnia obsługę składników hostingu Razor na serwerze w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b81a4-164"> provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="b81a4-165">Aktualizacje interfejsu użytkownika są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.</span><span class="sxs-lookup"><span data-stu-id="b81a4-165">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b81a4-166">Środowisko uruchomieniowe obsługuje wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera i zastosowanie aktualizacji interfejsu użytkownika wysyłanych przez serwer z powrotem do przeglądarki po uruchomieniu składników programu.</span><span class="sxs-lookup"><span data-stu-id="b81a4-166">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="b81a4-167">Połączenie używane przez Blazor Server program do komunikacji z przeglądarką jest również używane do obsługi wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b81a4-167">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="b81a4-168">![Blazor Serveruruchamia kod platformy .NET na serwerze i współdziała z Document Object Model na kliencie przez SignalR połączenie](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="b81a4-168">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="b81a4-169">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="b81a4-169">JavaScript interop</span></span>

<span data-ttu-id="b81a4-170">W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b81a4-170">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="b81a4-171">Składniki mogą korzystać z dowolnej biblioteki lub interfejsu API, który może być używany przez język JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b81a4-171">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="b81a4-172">Kod c# może wywołać kod JavaScript, a kod JavaScript może wywołać kod C#.</span><span class="sxs-lookup"><span data-stu-id="b81a4-172">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="b81a4-173">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="b81a4-173">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="b81a4-174">Udostępnianie kodu i .NET Standard</span><span class="sxs-lookup"><span data-stu-id="b81a4-174">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="b81a4-175">implementuje [.NET Standard 2,1](/dotnet/standard/net-standard), dzięki czemu Blazor projekty mogą odwoływać się do bibliotek, które są zgodne ze specyfikacją .NET Standard 2,1 lub wcześniejszą.</span><span class="sxs-lookup"><span data-stu-id="b81a4-175"> implements [.NET Standard 2.1](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard 2.1 or earlier specifications.</span></span> <span data-ttu-id="b81a4-176">.NET Standard jest formalną specyfikacją interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="b81a4-176">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="b81a4-177">Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor .NET Framework, .NET Core, Xamarin, mono i Unity.</span><span class="sxs-lookup"><span data-stu-id="b81a4-177">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="b81a4-178">Interfejsy API, które nie są stosowane w przeglądarce sieci Web (na przykład dostęp do systemu plików, otwieranie gniazda i wątkowość) throw <xref:System.PlatformNotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="b81a4-178">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b81a4-179">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b81a4-179">Additional resources</span></span>

* [<span data-ttu-id="b81a4-180">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="b81a4-180">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="b81a4-181">Przewodnik C#</span><span class="sxs-lookup"><span data-stu-id="b81a4-181">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="b81a4-182">HTML</span><span class="sxs-lookup"><span data-stu-id="b81a4-182">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="b81a4-183">[Firma Blazor Awesome](https://github.com/AdrienTorris/awesome-blazor) linki społeczności</span><span class="sxs-lookup"><span data-stu-id="b81a4-183">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
