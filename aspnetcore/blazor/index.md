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
ms.openlocfilehash: d42136de00ccc9b7565c5ae088e1ecf4560a63c5
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106885"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Wprowadzenie do ASP.NET Core Blazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

*Witamy Blazor !*

Blazor to platforma służąca do tworzenia interakcyjnego interfejsu użytkownika sieci Web po stronie klienta przy użyciu [platformy .NET](/dotnet/standard/tour):

* Twórz rozbudowane interaktywne interfejsów użytkownika przy użyciu [języka C#](/dotnet/csharp/) zamiast [języka JavaScript](https://www.javascript.com).
* Udostępnianie logiki aplikacji po stronie serwera i klienta zapisaną w środowisku .NET.
* Renderuj interfejs użytkownika jako HTML i CSS, aby obsługiwał szeroką przeglądarkę, w tym przeglądarki dla urządzeń przenośnych.
* Integruj z nowoczesnymi platformami hostingu, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Korzystanie z programu .NET do tworzenia aplikacji sieci Web po stronie klienta zapewnia następujące korzyści:

* Napisz kod w języku C# zamiast języka JavaScript.
* Skorzystaj z istniejącego ekosystemu .NET [bibliotek platformy .NET](/dotnet/standard/class-libraries).
* Udostępnianie logiki aplikacji między serwerem i klientem.
* Skorzystaj z programu. Wydajność, niezawodność i bezpieczeństwo sieci.
* Bądź na bieżąco z programem [Visual Studio](https://visualstudio.microsoft.com) w systemach Windows, Linux i macOS.
* Twórz w oparciu o wspólny zestaw języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.

## <a name="components"></a>Składniki

Blazor aplikacje są oparte na *składnikach*. Składnik w programie Blazor to element interfejsu użytkownika, taki jak strona, okno dialogowe lub formularz wprowadzania danych.

Składniki to klasy .NET C# wbudowane w [zestawy .NET](/dotnet/standard/assembly/) , które:

* Definiowanie elastycznej logiki renderowania interfejsu użytkownika.
* Obsługa zdarzeń użytkownika.
* Mogą być zagnieżdżane i ponownie używane.
* Mogą być udostępniane i dystrybuowane jako [ Razor biblioteki klas](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).

Klasa składnika jest zwykle zapisywana w formie [Razor](xref:mvc/views/razor) strony znaczników z `.razor` rozszerzeniem pliku. Składniki w programie Blazor są formalnie określane jako *Razor składniki*. Razor jest składnią służącą do łączenia znaczników HTML z kodem C# zaprojektowanym pod kątem produktywności dla deweloperów. Razor umożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą programowania [IntelliSense](/visualstudio/ide/using-intellisense) w programie Visual Studio. Razor Używane są również strony i MVC Razor . W przeciwieństwie do Razor stron i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta.

Blazor używa naturalnych tagów HTML dla kompozycji interfejsu użytkownika. Poniższy Razor znacznik ilustruje składnik ( `Dialog.razor` ), który wyświetla okno dialogowe i przetwarza zdarzenie, gdy użytkownik wybierze przycisk:

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

W poprzednim przykładzie jest to `OnYes` Metoda języka C# wyzwalana przez `onclick` zdarzenie przycisku. Tekst ( `ChildContent` ) i tytuł () okna dialogowego `Title` są dostarczane przez Poniższy składnik, który używa tego składnika w interfejsie użytkownika.

`Dialog`Składnik jest zagnieżdżony w innym składniku przy użyciu tagu HTML. W poniższym przykładzie `Index` składnik ( `Pages/Index.razor` ) używa poprzedniego `Dialog` składnika. `Title`Atrybut tagu przekazuje wartość tytułu do `Dialog` `Title` właściwości składnika.  `Dialog`Tekst składnika ( `ChildContent` ) jest ustawiany przez zawartość `<Dialog>` elementu. Po `Dialog` dodaniu składnika do składnika funkcja `Index` [IntelliSense w programie Visual Studio](/visualstudio/ide/using-intellisense) przyspiesza Programowanie przy użyciu składni i uzupełniania parametrów.

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

To okno dialogowe jest renderowane, gdy `Index` dostęp do składnika zostanie uzyskany w przeglądarce. Po wybraniu przycisku przez użytkownika konsola narzędzia deweloperskie w przeglądarce pokazuje komunikat zapisany przez `OnYes` metodę:

![Składnik okna dialogowego renderowany w przeglądarce zagnieżdżony wewnątrz składnika indeksu. Konsola narzędzia deweloperskie przeglądarki pokazuje komunikat zapisany przez kod C#, gdy użytkownik wybierze opcję tak! w interfejsie użytkownika.](index/_static/dialog.png)

Składniki są renderowane w pamięci podręcznej [Document Object Model (dom)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) przeglądarki o nazwie *drzewo renderowania*, która jest używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.

## Blazor WebAssembly

Blazor WebAssembly to [Struktura aplikacji jednostronicowej (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) do tworzenia interaktywnych aplikacji sieci Web po stronie klienta przy użyciu platformy .NET. Blazor WebAssembly używa otwartych standardów sieci Web bez wtyczek lub ponownego kompilowania kodu w innych językach. Blazor WebAssembly działa we wszystkich nowoczesnych przeglądarkach sieci Web, w tym w przeglądarkach dla urządzeń przenośnych.

Uruchamianie kodu platformy .NET wewnątrz przeglądarek sieci Web jest możliwe przez [zestaw webassembly](https://webassembly.org) (skrócony `wasm` ). Webassembly to kompaktowy format kodu bajtowego zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania. Webassembly to otwarty standard sieci Web, który jest obsługiwany w przeglądarkach sieci Web bez wtyczek.

Kod webassembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem języka JavaScript, nazywanego *współdziałaniem języka JavaScript*, często skracana do międzyoperacyjności *JavaScript Interop* lub *js*. Kod .NET wykonywany za pośrednictwem webassembly w przeglądarce jest uruchamiany w piaskownicy języka JavaScript przeglądarki z ochroną, którą piaskownica zapewnia przed złośliwymi działaniami na komputerze klienckim.

![::: No-Loc (Blazor webassembly)::: uruchamia kod .NET w przeglądarce z zestawem webassembly.](index/_static/blazor-webassembly.png)

Gdy Blazor WebAssembly aplikacja zostanie skompilowana i uruchomiona w przeglądarce:

* Pliki kodu C# i Razor pliki są kompilowane do zestawów .NET.
* Zestawy i [środowisko uruchomieniowe platformy .NET](/dotnet/framework/get-started/overview) są pobierane do przeglądarki.
* Blazor WebAssembly ładuje uruchomienia środowiska uruchomieniowego .NET i konfiguruje środowisko uruchomieniowe w celu załadowania zestawów dla aplikacji. Blazor WebAssemblyŚrodowisko uruchomieniowe używa międzyoperacyjnego języka JavaScript do obsługi operacji manipulowania Dom i interfejsu API przeglądarki.

Rozmiar opublikowanej aplikacji, jej *rozmiaru ładunku*, stanowi krytyczny współczynnik wydajności dla użyteczności aplikacji. Pobieranie dużej aplikacji do przeglądarki zajmuje stosunkowo dużo czasu, co zmniejsza środowisko użytkownika. Blazor WebAssembly optymalizuje rozmiar ładunku, aby skrócić czas pobierania:

::: moniker range=">= aspnetcore-5.0"

* Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany za pomocą elementu [dostosowującego języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-trimmer).
* Odpowiedzi HTTP są kompresowane.
* Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [konsolidator języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-linker).
* Odpowiedzi HTTP są kompresowane.
* Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.

::: moniker-end

## Blazor Server

Blazor oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika. *Blazor Server* zapewnia obsługę składników hostingu Razor na serwerze w aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

Środowisko uruchomieniowe pozostaje na serwerze i dojściach:

* Wykonywanie kodu w języku C# aplikacji.
* Wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera programu.
* Stosowanie aktualizacji interfejsu użytkownika do renderowanego składnika, który jest wysyłany z powrotem przez serwer.

Połączenie używane przez Blazor Server program do komunikacji z przeglądarką jest również używane do obsługi wywołań międzyoperacyjnych języka JavaScript.

![::: No-Loc (Blazor Server)::: uruchamia kod platformy .NET na serwerze i współdziała z Document Object Model na kliencie za pośrednictwem::: No-Loc (Sygnalizującer)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Międzyoperacyjność w języku JavaScript

W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z JavaScript. Składniki mogą korzystać z dowolnej biblioteki lub interfejsu API, który może być używany przez język JavaScript. Kod c# może [wywołać kod JavaScript](xref:blazor/call-javascript-from-dotnet), a kod JavaScript może [wywołać kod C#](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Udostępnianie kodu i .NET Standard

Blazor implementuje [.NET Standard](/dotnet/standard/net-standard), dzięki czemu Blazor projekty mogą odwoływać się do bibliotek, które są zgodne ze specyfikacjami .NET Standard. .NET Standard jest formalną specyfikacją interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET. Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor .NET Framework, .NET Core, Xamarin, mono i Unity.

Interfejsy API, które nie są stosowane w przeglądarce sieci Web (na przykład dostęp do systemu plików, otwieranie gniazda i wątkowość) throw <xref:System.PlatformNotSupportedException> .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zestaw webassembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [Przewodnik języka C#](/dotnet/csharp/)
* [Razor odwołanie do składni dla ASP.NET Core](xref:mvc/views/razor)
* [HTML](https://www.w3.org/html/)
* [Firma Blazor Awesome](https://github.com/AdrienTorris/awesome-blazor) linki społeczności
