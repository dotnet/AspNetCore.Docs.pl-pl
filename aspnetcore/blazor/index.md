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
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>Wprowadzenie do ASP.NET CoreBlazor

Autorstwa [Daniela Rotha](https://github.com/danroth27) i [Luke'a Lathama](https://github.com/guardrex)

*Witamy Blazorw !*

Blazorjest platformą do tworzenia interaktywnego interfejsu użytkownika sieci web po stronie klienta za pomocą platformy .NET:

* Tworzenie rozbudowanych interaktywnych interfejsów użytkownika przy użyciu języka C# zamiast javascript.
* Udostępnianie logiki aplikacji po stronie serwera i po stronie klienta napisanej w programie .NET.
* Renderuj interfejs użytkownika jako HTML i CSS, aby uzyskać szeroką obsługę przeglądarki, w tym przeglądarek mobilnych.
* Integracja z nowoczesnymi platformami hostingowymi, takimi jak [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Korzystanie z platformy .NET do tworzenia stron internetowych po stronie klienta oferuje następujące zalety:

* Napisz kod w języku C# zamiast JavaScript.
* Wykorzystaj istniejący ekosystem platformy .NET bibliotek .NET.
* Udostępnianie logiki aplikacji na serwerze i kliencie.
* Korzystaj z . Wydajność, niezawodność i bezpieczeństwo sieci NET.
* Zachowaj produktywność dzięki programowi Visual Studio w systemach Windows, Linux i macOS.
* Korzystaj ze wspólnego zestawu języków, struktur i narzędzi, które są stabilne, bogate w funkcje i łatwe w użyciu.

## <a name="components"></a>Składniki

Blazoraplikacje są oparte na *składnikach*. Składnik w Blazor jest elementem interfejsu użytkownika, takich jak strona, okno dialogowe lub formularz wprowadzania danych.

Składniki są klasami .NET wbudowanymi w zestawy .NET, które:

* Zdefiniuj logikę renderowania elastycznego interfejsu użytkownika.
* Obsługa zdarzeń użytkownika.
* Może być zagnieżdżony i ponownie zagnieżdżony.
* Może być współużytkowany i dystrybuowany jako [biblioteki klas Razor](xref:razor-pages/ui-class) lub [pakiety NuGet](/nuget/what-is-nuget).

Klasa składnika jest zwykle zapisywana w formie strony znaczników [Razor](xref:mvc/views/razor) z rozszerzeniem pliku *.brzytwa.* Komponenty Blazor są formalnie określane jako *komponenty Razor*. Razor to składnia do łączenia znaczników HTML z kodem C# przeznaczonym dla produktywności dewelopera. Razor umożliwia przełączanie między znacznikami HTML i C# w tym samym pliku z obsługą [IntelliSense.](/visualstudio/ide/using-intellisense) Razor Pages i MVC również używać Razor. W przeciwieństwie do stron Razor i MVC, które są zbudowane wokół modelu żądania/odpowiedzi, składniki są używane specjalnie dla logiki interfejsu użytkownika po stronie klienta i kompozycji.

Następujące znaczniki Razor demonstruje składnik (*Dialog.brzytwa*), który może być zagnieżdżony w innym składniku:

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

Zawartość treści okna dialogowego (`ChildContent``Title`) i tytuł ( ) są dostarczane przez składnik, który używa tego składnika w jego interfejsie użytkownika. `OnYes`jest metodą języka C# wyzwalaną `onclick` przez zdarzenie przycisku.

Blazorużywa naturalnych znaczników HTML dla kompozycji interfejsu użytkownika. Elementy HTML określają komponenty, a atrybuty znacznika przekazują wartości do właściwości składnika.

W poniższym przykładzie `Index` komponent `Dialog` używa składnika. `ChildContent`i `Title` są ustawiane przez atrybuty i zawartość `<Dialog>` elementu.

*Index.brzytwa*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Okno dialogowe jest renderowane, gdy element nadrzędny (*Index.brzytwa*) jest dostępny w przeglądarce:

![Składnik okna dialogowego renderowany w przeglądarce](index/_static/dialog.png)

Gdy ten składnik jest używany w aplikacji, IntelliSense w [programie Visual Studio](/visualstudio/ide/using-intellisense) i Visual Studio [Code](https://code.visualstudio.com/docs/editor/intellisense) przyspiesza tworzenie ze składnią i zakończeniem parametrów.

Komponenty renderowania w pamięci reprezentacji modelu obiektu dokumentu przeglądarki (DOM) o nazwie *drzewa renderowania*, który jest używany do aktualizacji interfejsu użytkownika w sposób elastyczny i wydajny.

## <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly (WebAssembly)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly to jednostronicowa struktura aplikacji do tworzenia interaktywnych aplikacji sieci web po stronie klienta za pomocą platformy .NET. BlazorWebAssembly wykorzystuje otwarte standardy internetowe bez wtyczek lub transpilacji kodu i działa we wszystkich nowoczesnych przeglądarkach internetowych, w tym w przeglądarkach mobilnych.

Uruchamianie kodu .NET w przeglądarkach internetowych jest możliwe dzięki [WebAssembly](https://webassembly.org) (w skrócie *wasm).* WebAssembly to kompaktowy format bajtowy zoptymalizowany pod kątem szybkiego pobierania i maksymalnej szybkości wykonywania. WebAssembly jest otwartym standardem internetowym i obsługiwanym w przeglądarkach internetowych bez wtyczek.

Kod WebAssembly może uzyskać dostęp do pełnej funkcjonalności przeglądarki za pośrednictwem Języka JavaScript, o nazwie *Interoperacyjność JavaScript* (lub *JavaScript interop*). Kod .NET wykonywany za pośrednictwem systemu WebAssembly w przeglądarce działa w piaskownicy JavaScript przeglądarki z zabezpieczeniami zapewnianych przez piaskownicę przed szkodliwymi działaniami na komputerze klienckim.

![BlazorWebAssembly uruchamia kod .NET w przeglądarce za pomocą webassembly.](index/_static/blazor-webassembly.png)

Gdy Blazor aplikacja WebAssembly jest zbudowana i uruchamiana w przeglądarce:

* Pliki kodu języka C# i pliki Razor są kompilowane w zestawy .NET.
* Zestawy i środowisko uruchomieniowe platformy .NET są pobierane do przeglądarki.
* BlazorWebAssembly bootstraps .NET środowiska uruchomieniowego i konfiguruje środowisko uruchomieniowe, aby załadować zestawy dla aplikacji. Środowisko Blazor wykonawcze WebAssembly używa javascript interop do obsługi manipulacji DOM i wywołania interfejsu API przeglądarki.

Rozmiar opublikowanej aplikacji, jej *rozmiar ładunku*, jest krytycznym czynnikiem wydajności dla użyteczności aplikacji. Duża aplikacja zajmuje stosunkowo dużo czasu, aby pobrać do przeglądarki, co zmniejsza komfort użytkownika. BlazorWebAssembly optymalizuje rozmiar ładunku, aby skrócić czas pobierania:

* Nieużywany kod jest usuwany z aplikacji, gdy jest publikowany przez [linker języka pośredniego (IL).](xref:host-and-deploy/blazor/configure-linker)
* Odpowiedzi HTTP są kompresowane.
* Środowisko uruchomieniowe i zestawy .NET są buforowane w przeglądarce.

## <a name="opno-locblazor-server"></a>BlazorSerwera

Blazoroddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika. BlazorSerwer zapewnia obsługę hostowania składników Razor na serwerze w aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika są [SignalR](xref:signalr/introduction) obsługiwane przez połączenie.

Środowisko wykonawcze obsługuje wysyłanie zdarzeń interfejsu użytkownika z przeglądarki do serwera i stosuje aktualizacje interfejsu użytkownika wysyłane przez serwer z powrotem do przeglądarki po uruchomieniu składników.

Połączenie używane Blazor przez serwer do komunikowania się z przeglądarką jest również używane do obsługi wywołań interop JavaScript.

![BlazorSerwer uruchamia kod platformy .NET na serwerze i współdziała SignalR z modelem obiektu dokumentu na kliencie za pomocą połączenia](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Międzyoperacyjność w języku JavaScript

W przypadku aplikacji, które wymagają bibliotek JavaScript innych firm i dostępu do interfejsów API przeglądarki, składniki współdziałają z javascriptem. Składniki mogą używać dowolnej biblioteki lub interfejsu API, z których można korzystać w języku JavaScript. Kod języka C# można wywołać w kodzie JavaScript, a kod JavaScript można wywołać w kodzie języka C#. Aby uzyskać więcej informacji zobacz następujące artykuły:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Udostępnianie kodu i standard .NET

Blazorimplementuje [.NET Standard 2.0](/dotnet/standard/net-standard). .NET Standard to formalna specyfikacja interfejsów API platformy .NET, które są wspólne dla implementacji platformy .NET. Biblioteki klas .NET Standard mogą być współużytkowane przez różne platformy .NET, takie jak Blazor, .NET Framework, .NET Core, Xamarin, Mono i Unity.

Interfejsy API, które nie mają zastosowania wewnątrz przeglądarki internetowej (na przykład uzyskiwanie dostępu do systemu <xref:System.PlatformNotSupportedException>plików, otwieranie gniazda i wątkowanie) są rzutowe .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [WebAssembly (WebAssembly)](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [C# Przewodnik](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Niesamowite Blazor ](https://github.com/AdrienTorris/awesome-blazor) linki społecznościowe
