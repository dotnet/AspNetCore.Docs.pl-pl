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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: 086629f88ad04faed812bd16683e7d073f3e8ede
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122077"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Wprowadzenie do ASP.NET CoreBlazor

Autorzy [Daniel Roth](https://github.com/danroth27) i [Luke Latham](https://github.com/guardrex)

*Witamy Blazor !*

Blazorto platforma służąca do tworzenia interakcyjnego interfejsu użytkownika sieci Web po stronie klienta przy użyciu platformy .NET:

* Twórz rozbudowane interaktywne interfejsów użytkownika przy użyciu języka C# zamiast języka JavaScript.
* Udostępnianie logiki aplikacji po stronie serwera i klienta zapisaną w środowisku .NET.
* Renderuj interfejs użytkownika jako HTML i CSS, aby obsługiwał szeroką przeglądarkę, w tym przeglądarki dla urządzeń przenośnych.
* Integruj z nowoczesnymi platformami hostingu, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Korzystanie z programu .NET do tworzenia aplikacji sieci Web po stronie klienta zapewnia następujące korzyści:

* Napisz kod w języku C# zamiast języka JavaScript.
* Skorzystaj z istniejącego ekosystemu .NET bibliotek platformy .NET.
* Udostępnianie logiki aplikacji między serwerem i klientem.
* Skorzystaj z programu. Wydajność, niezawodność i bezpieczeństwo sieci.
* Bądź na bieżąco z programem Visual Studio w systemach Windows, Linux i macOS.
* Twórz w oparciu o wspólny zestaw języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.

## <a name="components"></a>Składniki

Blazoraplikacje są oparte na *składnikach*. Składnik w programie Blazor to element interfejsu użytkownika, taki jak strona, okno dialogowe lub formularz wprowadzania danych.

Składniki to klasy .NET wbudowane w zestawy .NET, które:

* Definiowanie elastycznej logiki renderowania interfejsu użytkownika.
* Obsługa zdarzeń użytkownika.
* Mogą być zagnieżdżane i ponownie używane.
* Mogą być udostępniane i dystrybuowane jako [ Razor biblioteki klas](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).

Klasa składnika jest zwykle zapisywana w formie [Razor](xref:mvc/views/razor) strony znaczników z rozszerzeniem *. Razor* . Składniki w programie Blazor są formalnie określane jako * Razor składniki*. Razorjest składnią służącą do łączenia znaczników HTML z kodem C# zaprojektowanym pod kątem produktywności dla deweloperów. Razorumożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą [IntelliSense](/visualstudio/ide/using-intellisense) . RazorUżywane są również strony i MVC Razor . W przeciwieństwie do Razor stron i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta.

Poniższy Razor znacznik ilustruje składnik (*dialog. Razor*), który może być zagnieżdżony w innym składniku:

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

Zawartość ( `ChildContent` ) i tytuł () okna dialogowego `Title` są udostępniane przez składnik, który używa tego składnika w interfejsie użytkownika. `OnYes`jest metodą języka C# wyzwalaną przez zdarzenie przycisku `onclick` .

Blazorużywa naturalnych tagów HTML dla kompozycji interfejsu użytkownika. Elementy HTML określają składniki, a atrybuty znacznika przechodzą wartości do właściwości składnika.

W poniższym przykładzie `Index` składnik używa `Dialog` składnika. `ChildContent`i `Title` są ustawiane przez atrybuty i zawartość `<Dialog>` elementu.

*Index. Razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Okno dialogowe jest renderowane po uzyskaniu dostępu do elementu nadrzędnego (*index. Razor*) w przeglądarce:

![Składnik okna dialogowego renderowany w przeglądarce](index/_static/dialog.png)

Gdy ten składnik jest używany w aplikacji, funkcja IntelliSense w programie [Visual Studio](/visualstudio/ide/using-intellisense) i [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) przyspiesza Programowanie przy użyciu składni i uzupełniania parametrów.

Składniki są renderowane w pamięci podręcznej Document Object Model (DOM) przeglądarki o nazwie *drzewo renderowania*, która jest używana do aktualizowania interfejsu użytkownika w elastyczny i wydajny sposób.

## <a name="blazor-webassembly"></a>BlazorZestaw webassembly

BlazorWebassembly to jednostronicowa platforma aplikacji służąca do tworzenia interaktywnych aplikacji sieci Web po stronie klienta przy użyciu platformy .NET. BlazorZestaw webassembly używa otwartych standardów sieci Web bez wtyczek lub kodu transpilation i działa we wszystkich nowoczesnych przeglądarkach sieci Web, w tym w przeglądarkach mobilnych.

Uruchamianie kodu platformy .NET wewnątrz przeglądarek sieci Web jest możliwe przez [zestaw webassembly](https://webassembly.org) (skrócony *wasm*). Webassembly to kompaktowy format kodu bajtowego zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania. Webassembly to otwarty standard sieci Web, który jest obsługiwany w przeglądarkach sieci Web bez wtyczek.

Kod webassembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem języka JavaScript, nazywanego *współdziałaniem JavaScript* (lub *międzyoperacyjną JavaScript*). Kod .NET wykonywany za pośrednictwem webassembly w przeglądarce jest uruchamiany w piaskownicy języka JavaScript przeglądarki z ochroną, którą piaskownica zapewnia przed złośliwymi działaniami na komputerze klienckim.

![BlazorZestaw webassembly uruchamia kod platformy .NET w przeglądarce z zestawem webassembly.](index/_static/blazor-webassembly.png)

Gdy Blazor aplikacja webassembly zostanie skompilowana i uruchomiona w przeglądarce:

* Pliki kodu C# i Razor pliki są kompilowane do zestawów .NET.
* Zestawy i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.
* BlazorZestaw webassembly Bootstrap środowisko uruchomieniowe platformy .NET i konfiguruje środowisko uruchomieniowe w celu załadowania zestawów dla aplikacji. BlazorŚrodowisko uruchomieniowe webassembly używa interfejsu JavaScript Interop do obsługi operacji manipulowania modelem Dom i interfejsów API przeglądarki.

Rozmiar opublikowanej aplikacji, jej *rozmiaru ładunku*, jest krytycznym czynnikiem wydajności dla useability aplikacji. Pobieranie dużej aplikacji do przeglądarki zajmuje stosunkowo dużo czasu, co zmniejsza środowisko użytkownika. BlazorZestaw webassembly optymalizuje rozmiar ładunku, aby skrócić czas pobierania:

* Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [konsolidator języka pośredniego (IL)](xref:blazor/host-and-deploy/configure-linker).
* Odpowiedzi HTTP są kompresowane.
* Środowisko uruchomieniowe platformy .NET i zestawy są buforowane w przeglądarce.

## <a name="blazor-server"></a>BlazorServer

Blazoroddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika. BlazorSerwer zapewnia obsługę składników hostingu Razor na serwerze w aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika są obsługiwane przez [SignalR](xref:signalr/introduction) połączenie.

Środowisko uruchomieniowe obsługuje wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera i zastosowanie aktualizacji interfejsu użytkownika wysyłanych przez serwer z powrotem do przeglądarki po uruchomieniu składników programu.

Połączenie używane przez Blazor serwer do komunikacji z przeglądarką jest również używane do obsługi wywołań międzyoperacyjnych języka JavaScript.

![BlazorSerwer uruchamia kod platformy .NET na serwerze i współdziała z Document Object Model na kliencie przez SignalR połączenie](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Międzyoperacyjność w języku JavaScript

W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z JavaScript. Składniki mogą korzystać z dowolnej biblioteki lub interfejsu API, który może być używany przez język JavaScript. Kod c# może wywołać kod JavaScript, a kod JavaScript może wywołać kod C#. Aby uzyskać więcej informacji, zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Udostępnianie kodu i .NET Standard

Blazorimplementuje [.NET Standard 2,1](/dotnet/standard/net-standard), dzięki czemu Blazor projekty mogą odwoływać się do bibliotek, które są zgodne ze specyfikacją .NET Standard 2,1 lub wcześniejszą. .NET Standard jest formalną specyfikacją interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET. Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor .NET Framework, .NET Core, Xamarin, mono i Unity.

Interfejsy API, które nie są stosowane w przeglądarce sieci Web (na przykład dostęp do systemu plików, otwieranie gniazda i wątkowość) throw <xref:System.PlatformNotSupportedException> .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zestaw webassembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [Przewodnik C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Firma Blazor Awesome](https://github.com/AdrienTorris/awesome-blazor) linki społeczności
