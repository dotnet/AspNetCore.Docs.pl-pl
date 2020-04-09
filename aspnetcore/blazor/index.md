---
title: Wprowadzenie do ASP.NET CoreBlazor
author: guardrex
description: Zapoznaj się ASP.NET BlazorCore , sposób tworzenia interaktywnego interfejsu użytkownika sieci web po stronie klienta za pomocą platformy .NET w aplikacji core ASP.NET.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405953"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a><span data-ttu-id="377cc-103">Wprowadzenie do ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="377cc-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="377cc-104">Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="377cc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="377cc-105">*Witamy Blazorw !*</span><span class="sxs-lookup"><span data-stu-id="377cc-105">*Welcome to Blazor!*</span></span>

Blazor<span data-ttu-id="377cc-106">jest platformą do tworzenia interaktywnego interfejsu użytkownika sieci web po stronie klienta za pomocą platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="377cc-106"> is a framework for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="377cc-107">Tworzenie rozbudowanych interaktywnych interfejsów użytkownika przy użyciu języka C# zamiast javascript.</span><span class="sxs-lookup"><span data-stu-id="377cc-107">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="377cc-108">Udostępnianie logiki aplikacji po stronie serwera i po stronie klienta napisanej w programie .NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="377cc-109">Renderuj interfejs użytkownika jako HTML i CSS, aby uzyskać szeroką obsługę przeglądarki, w tym przeglądarek mobilnych.</span><span class="sxs-lookup"><span data-stu-id="377cc-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="377cc-110">Integracja z nowoczesnymi platformami hostingowymi, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span><span class="sxs-lookup"><span data-stu-id="377cc-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="377cc-111">Korzystanie z platformy .NET do tworzenia stron internetowych po stronie klienta oferuje następujące zalety:</span><span class="sxs-lookup"><span data-stu-id="377cc-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="377cc-112">Napisz kod w języku C# zamiast JavaScript.</span><span class="sxs-lookup"><span data-stu-id="377cc-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="377cc-113">Wykorzystaj istniejący ekosystem platformy .NET bibliotek .NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-113">Leverage the existing .NET ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="377cc-114">Udostępnianie logiki aplikacji na serwerze i kliencie.</span><span class="sxs-lookup"><span data-stu-id="377cc-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="377cc-115">Korzystaj z . Wydajność, niezawodność i bezpieczeństwo sieci NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="377cc-116">Zachowaj produktywność dzięki programowi Visual Studio w systemach Windows, Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="377cc-116">Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="377cc-117">Korzystaj ze wspólnego zestawu języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.</span><span class="sxs-lookup"><span data-stu-id="377cc-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="377cc-118">Składniki</span><span class="sxs-lookup"><span data-stu-id="377cc-118">Components</span></span>

Blazor<span data-ttu-id="377cc-119">aplikacje są oparte na *składnikach*.</span><span class="sxs-lookup"><span data-stu-id="377cc-119"> apps are based on *components*.</span></span> <span data-ttu-id="377cc-120">Składnik w Blazor jest elementem interfejsu użytkownika, takich jak strona, okno dialogowe lub formularz wprowadzania danych.</span><span class="sxs-lookup"><span data-stu-id="377cc-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="377cc-121">Składniki są klasami .NET wbudowanymi w zestawy .NET, które:</span><span class="sxs-lookup"><span data-stu-id="377cc-121">Components are .NET classes built into .NET assemblies that:</span></span>

* <span data-ttu-id="377cc-122">Zdefiniuj logikę renderowania elastycznego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="377cc-123">Obsługa zdarzeń użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-123">Handle user events.</span></span>
* <span data-ttu-id="377cc-124">Może być zagnieżdżony i ponownie zagnieżdżony.</span><span class="sxs-lookup"><span data-stu-id="377cc-124">Can be nested and reused.</span></span>
* <span data-ttu-id="377cc-125">Może być współużytkowany i dystrybuowany jako [biblioteki klas Razor](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).</span><span class="sxs-lookup"><span data-stu-id="377cc-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="377cc-126">Klasa składnika jest zwykle zapisywana w formie strony znaczników [Razor](xref:mvc/views/razor) z rozszerzeniem pliku *.brzytwa.*</span><span class="sxs-lookup"><span data-stu-id="377cc-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a *.razor* file extension.</span></span> <span data-ttu-id="377cc-127">Komponenty Blazor są formalnie określane jako *komponenty Razor*.</span><span class="sxs-lookup"><span data-stu-id="377cc-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="377cc-128">Razor to składnia do łączenia znaczników HTML z kodem C# przeznaczonym dla produktywności dewelopera.</span><span class="sxs-lookup"><span data-stu-id="377cc-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="377cc-129">Razor umożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą [IntelliSense.](/visualstudio/ide/using-intellisense)</span><span class="sxs-lookup"><span data-stu-id="377cc-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="377cc-130">Razor Pages i MVC również używać Razor.</span><span class="sxs-lookup"><span data-stu-id="377cc-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="377cc-131">W przeciwieństwie do stron Razor i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta i kompozycji.</span><span class="sxs-lookup"><span data-stu-id="377cc-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="377cc-132">Następujące znaczniki Razor demonstruje składnik (*Dialog.brzytwa*), który może być zagnieżdżony w innym składniku:</span><span class="sxs-lookup"><span data-stu-id="377cc-132">The following Razor markup demonstrates a component (*Dialog.razor*), which can be nested within another component:</span></span>

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

<span data-ttu-id="377cc-133">Zawartość treści okna dialogowego (`ChildContent``Title`) i tytuł ( ) są dostarczane przez składnik, który używa tego składnika w jego interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-133">The dialog's body content (`ChildContent`) and title (`Title`) are provided by the component that uses this component in its UI.</span></span> <span data-ttu-id="377cc-134">`OnYes`jest metodą języka C# wyzwalaną `onclick` przez zdarzenie przycisku.</span><span class="sxs-lookup"><span data-stu-id="377cc-134">`OnYes` is a C# method triggered by the button's `onclick` event.</span></span>

Blazor<span data-ttu-id="377cc-135">używa naturalnych znaczników HTML dla kompozycji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-135"> uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="377cc-136">Elementy HTML określają komponenty, a atrybuty znacznika przekazują wartości do właściwości składnika.</span><span class="sxs-lookup"><span data-stu-id="377cc-136">HTML elements specify components, and a tag's attributes pass values to a component's properties.</span></span>

<span data-ttu-id="377cc-137">W poniższym przykładzie `Index` komponent `Dialog` używa składnika.</span><span class="sxs-lookup"><span data-stu-id="377cc-137">In the following example, the `Index` component uses the `Dialog` component.</span></span> <span data-ttu-id="377cc-138">`ChildContent`i `Title` są ustawiane przez atrybuty i zawartość `<Dialog>` elementu.</span><span class="sxs-lookup"><span data-stu-id="377cc-138">`ChildContent` and `Title` are set by the attributes and content of the `<Dialog>` element.</span></span>

<span data-ttu-id="377cc-139">*Index.brzytwa*:</span><span class="sxs-lookup"><span data-stu-id="377cc-139">*Index.razor*:</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="377cc-140">Okno dialogowe jest renderowane, gdy element nadrzędny (*Index.brzytwa*) jest dostępny w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="377cc-140">The dialog is rendered when the parent (*Index.razor*) is accessed in a browser:</span></span>

![Składnik okna dialogowego renderowany w przeglądarce](index/_static/dialog.png)

<span data-ttu-id="377cc-142">Gdy ten składnik jest używany w aplikacji, IntelliSense w [programie Visual Studio](/visualstudio/ide/using-intellisense) i Visual Studio [Code](https://code.visualstudio.com/docs/editor/intellisense) przyspiesza tworzenie ze składnią i zakończeniem parametrów.</span><span class="sxs-lookup"><span data-stu-id="377cc-142">When this component is used in the app, IntelliSense in [Visual Studio](/visualstudio/ide/using-intellisense) and [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="377cc-143">Komponenty renderowania w pamięci reprezentacji modelu obiektu dokumentu przeglądarki (DOM) o nazwie *drzewa renderowania*, który jest używany do aktualizacji interfejsu użytkownika w sposób elastyczny i wydajny.</span><span class="sxs-lookup"><span data-stu-id="377cc-143">Components render into an in-memory representation of the browser's Document Object Model (DOM) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="377cc-144">WebAssembly (WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="377cc-144"> WebAssembly</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="377cc-145">WebAssembly to jednostronicowa struktura aplikacji do tworzenia interaktywnych aplikacji sieci web po stronie klienta za pomocą platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-145"> WebAssembly is a single-page app framework for building interactive client-side web apps with .NET.</span></span> Blazor<span data-ttu-id="377cc-146">WebAssembly wykorzystuje otwarte standardy internetowe bez wtyczek lub transpilacji kodu i działa we wszystkich nowoczesnych przeglądarkach internetowych, w tym w przeglądarkach mobilnych.</span><span class="sxs-lookup"><span data-stu-id="377cc-146"> WebAssembly uses open web standards without plugins or code transpilation and works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="377cc-147">Uruchamianie kodu .NET w przeglądarkach internetowych jest możliwe dzięki [WebAssembly](https://webassembly.org) (w skrócie *wasm).*</span><span class="sxs-lookup"><span data-stu-id="377cc-147">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="377cc-148">WebAssembly to kompaktowy format bajtowy zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania.</span><span class="sxs-lookup"><span data-stu-id="377cc-148">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="377cc-149">WebAssembly jest otwartym standardem internetowym i obsługiwanym w przeglądarkach internetowych bez wtyczek.</span><span class="sxs-lookup"><span data-stu-id="377cc-149">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="377cc-150">Kod WebAssembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem Języka JavaScript, o nazwie *Interoperacyjność JavaScript* (lub *JavaScript interop*).</span><span class="sxs-lookup"><span data-stu-id="377cc-150">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* (or *JavaScript interop*).</span></span> <span data-ttu-id="377cc-151">Kod .NET wykonywany za pośrednictwem systemu WebAssembly w przeglądarce działa w piaskownicy JavaScript przeglądarki z zabezpieczeniami zapewnianych przez piaskownicę przed szkodliwymi działaniami na komputerze klienckim.</span><span class="sxs-lookup"><span data-stu-id="377cc-151">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

<span data-ttu-id="377cc-152">![BlazorWebAssembly uruchamia kod .NET w przeglądarce za pomocą webassembly.](index/_static/blazor-webassembly.png)</span><span class="sxs-lookup"><span data-stu-id="377cc-152">![Blazor WebAssembly runs .NET code in the browser with WebAssembly.](index/_static/blazor-webassembly.png)</span></span>

<span data-ttu-id="377cc-153">Gdy Blazor aplikacja WebAssembly jest zbudowana i uruchamiana w przeglądarce:</span><span class="sxs-lookup"><span data-stu-id="377cc-153">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="377cc-154">Pliki kodu języka C# i pliki Razor są kompilowane w zestawy .NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-154">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="377cc-155">Zestawy i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="377cc-155">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* Blazor<span data-ttu-id="377cc-156">WebAssembly bootstraps .NET środowiska uruchomieniowego i konfiguruje środowisko uruchomieniowe, aby załadować zestawy dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="377cc-156"> WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="377cc-157">Środowisko Blazor wykonawcze WebAssembly używa javascript interop do obsługi manipulacji DOM i wywołania interfejsu API przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="377cc-157">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="377cc-158">Rozmiar opublikowanej aplikacji, jej *rozmiar ładunku*, jest krytycznym czynnikiem wydajności dla użyteczności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="377cc-158">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="377cc-159">Duża aplikacja zajmuje stosunkowo dużo czasu, aby pobrać do przeglądarki, co zmniejsza komfort użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-159">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> Blazor<span data-ttu-id="377cc-160">WebAssembly optymalizuje rozmiar ładunku, aby skrócić czas pobierania:</span><span class="sxs-lookup"><span data-stu-id="377cc-160"> WebAssembly optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="377cc-161">Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [linker języka pośredniego (IL).](xref:host-and-deploy/blazor/configure-linker)</span><span class="sxs-lookup"><span data-stu-id="377cc-161">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>
* <span data-ttu-id="377cc-162">Odpowiedzi HTTP są kompresowane.</span><span class="sxs-lookup"><span data-stu-id="377cc-162">HTTP responses are compressed.</span></span>
* <span data-ttu-id="377cc-163">Środowisko uruchomieniowe i zestawy .NET są buforowane w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="377cc-163">The .NET runtime and assemblies are cached in the browser.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="377cc-164">Serwera</span><span class="sxs-lookup"><span data-stu-id="377cc-164"> Server</span></span>

Blazor<span data-ttu-id="377cc-165">oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="377cc-165"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="377cc-166">Serwer zapewnia obsługę hostowania składników Razor na serwerze w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="377cc-166"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="377cc-167">Aktualizacje interfejsu użytkownika są [SignalR](xref:signalr/introduction) obsługiwane przez połączenie.</span><span class="sxs-lookup"><span data-stu-id="377cc-167">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="377cc-168">Środowisko wykonawcze obsługuje wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera i stosuje aktualizacje interfejsu użytkownika wysyłane przez serwer z powrotem do przeglądarki po uruchomieniu składników.</span><span class="sxs-lookup"><span data-stu-id="377cc-168">The runtime handles sending UI events from the browser to the server and applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="377cc-169">Połączenie używane Blazor przez serwer do komunikowania się z przeglądarką jest również używane do obsługi wywołań interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="377cc-169">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

<span data-ttu-id="377cc-170">![BlazorSerwer uruchamia kod platformy .NET na serwerze i współdziała SignalR z modelem obiektu dokumentu na kliencie za pomocą połączenia](index/_static/blazor-server.png)</span><span class="sxs-lookup"><span data-stu-id="377cc-170">![Blazor Server runs .NET code on the server and interacts with the Document Object Model on the client over a SignalR connection](index/_static/blazor-server.png)</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="377cc-171">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="377cc-171">JavaScript interop</span></span>

<span data-ttu-id="377cc-172">W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z javascriptem.</span><span class="sxs-lookup"><span data-stu-id="377cc-172">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="377cc-173">Składniki mogą używać dowolnej biblioteki lub interfejsu API, z których można korzystać w języku JavaScript.</span><span class="sxs-lookup"><span data-stu-id="377cc-173">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="377cc-174">Kod języka C# można wywołać w kodzie JavaScript, a kod JavaScript można wywołać w kodzie języka C#.</span><span class="sxs-lookup"><span data-stu-id="377cc-174">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="377cc-175">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="377cc-175">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="377cc-176">Udostępnianie kodu i standard .NET</span><span class="sxs-lookup"><span data-stu-id="377cc-176">Code sharing and .NET Standard</span></span>

Blazor<span data-ttu-id="377cc-177">implementuje [.NET Standard 2.0](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="377cc-177"> implements [.NET Standard 2.0](/dotnet/standard/net-standard).</span></span> <span data-ttu-id="377cc-178">.NET Standard to formalna specyfikacja interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="377cc-178">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="377cc-179">Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor, .NET Framework, .NET Core, Xamarin, Mono i Unity.</span><span class="sxs-lookup"><span data-stu-id="377cc-179">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="377cc-180">Interfejsy API, które nie mają zastosowania wewnątrz przeglądarki internetowej (na przykład uzyskiwanie dostępu do systemu <xref:System.PlatformNotSupportedException>plików, otwieranie gniazda i wątkowanie) są rzutowe .</span><span class="sxs-lookup"><span data-stu-id="377cc-180">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="377cc-181">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="377cc-181">Additional resources</span></span>

* [<span data-ttu-id="377cc-182">WebAssembly (WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="377cc-182">WebAssembly</span></span>](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [<span data-ttu-id="377cc-183">C# Przewodnik</span><span class="sxs-lookup"><span data-stu-id="377cc-183">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="377cc-184">HTML</span><span class="sxs-lookup"><span data-stu-id="377cc-184">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="377cc-185">[Niesamowite Blazor ](https://github.com/AdrienTorris/awesome-blazor) linki społecznościowe</span><span class="sxs-lookup"><span data-stu-id="377cc-185">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
