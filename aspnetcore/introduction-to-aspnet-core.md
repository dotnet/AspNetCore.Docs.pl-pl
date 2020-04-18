---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Wprowadzenie do ASP.NET Core, wieloplatformowej, wydajnej struktury open source do tworzenia nowoczesnych aplikacji połączonych z Chmurą, połączonych z Internetem.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615946"
---
# <a name="introduction-to-aspnet-core"></a>Wprowadzenie do platformy ASP.NET Core

[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) i [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core to wieloplatformowa, wydajna struktura [open source](https://github.com/dotnet/aspnetcore) do tworzenia nowoczesnych aplikacji połączonych z Chmurą i połączonych z Internetem. Platforma ASP.NET Core umożliwia:

* Tworzenie aplikacji i usług internetowych, aplikacji [Internetu rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i zaplecza mobilnego.
* Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.
* Wdrażanie w chmurze lub lokalnie.
* Uruchom na [.NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Dlaczego warto wybrać ASP.NET Core?

Miliony programistów używają lub używają [ASP.NET 4.x](/aspnet/overview) do tworzenia aplikacji internetowych. ASP.NET Core to przeprojektowanie ASP.NET 4.x, w tym zmian architektonicznych, które skutkują szczuplejszą, bardziej modułową strukturą.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core

Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):

* Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.
* [Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.
* [Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.
* Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.
* [Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.
* [Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.

## <a name="client-side-development"></a>Programowanie po stronie klienta

ASP.NET Core bezproblemowo integruje się z popularnymi strukturami i bibliotekami po stronie klienta, w tym [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)i [Bootstrap.](https://getbootstrap.com/) Aby uzyskać więcej <xref:blazor/index> informacji, zobacz i tematy pokrewne w obszarze *Rozwój po stronie klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET podstawowe struktury docelowe

ASP.NET Core 3.x i nowszych może być tylko ukierunkowane .NET Core. Ogólnie rzecz biorąc ASP.NET Core składa się z bibliotek [.NET Standard.](/dotnet/standard/net-standard) Biblioteki napisane za pomocą platformy .NET Standard 2.0 są uruchamiane na dowolnej [platformie .NET, która implementuje program .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem. Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:

* Wieloplatformowość. Działa w systemach Windows, macOS i Linux.
* Większa wydajność
* [Przechowywanie wersji obok siebie](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Nowe interfejsy API
* Kod open source

## <a name="recommended-learning-path"></a>Zalecana ścieżka szkoleniowa

Firma Microsoft zaleca następującą sekwencję samouczków dla wprowadzenia do tworzenia aplikacji ASP.NET Core:

1. Postępuj zgodnie z samouczkiem dla typu aplikacji, który chcesz opracować lub utrzymać.

   |Typ aplikacji  |Scenariusz  |Samouczek  |
   |----------|----------|----------|
   |Aplikacja internetowa                   | Nowe tworzenie interfejsu użytkownika sieci web po stronie serwera |[Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplikacja internetowa                   | Obsługa aplikacji MVC |[Wprowadzenie do wzorca MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Aplikacja internetowa                   | Tworzenie interfejsu użytkownika sieci web po stronie klienta |[Wprowadzenie do blazora](xref:tutorials/first-blazor-app) |
   |Interfejs API sieci Web                   | Restful usług HTTP |[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger; |
   |Aplikacja Do wywołania procedury zdalnej | Usługi oparte na kontraktach przy użyciu buforów protokołów |[Wprowadzenie do usługi gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplikacja czasu rzeczywistego             | Dwukierunkowa komunikacja między serwerami a podłączonymi klientami |[Wprowadzenie do usługi SignalR](xref:tutorials/signalr) |

1. Postępuj zgodnie z samouczkiem, który pokazuje, jak zrobić podstawowy dostęp do danych.

   |Scenariusz  |Samouczek  |
   |----------|----------|
   |Nowy rozwój        |[Platforma Razor Pages z platformą Entity Framework Core](xref:data/ef-rp/intro) |
   |Obsługa aplikacji MVC |[Wzorzec MVC z platformą Entity Framework Core](xref:data/ef-mvc/intro) |

1. Przeczytaj omówienie ASP.NET [podstawowe podstawy,](xref:fundamentals/index) które mają zastosowanie do wszystkich typów aplikacji.

1. Przejrzyj spis treści w poszukiwaniu innych interesujących tematów.

&dagger;Istnieje również [interaktywny samouczek interfejsu API sieci.](/learn/modules/build-web-api-net-core) Nie jest wymagana lokalna instalacja narzędzi programistycznych. Kod jest uruchamiany w [usłudze Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w przeglądarce, a [curl](https://curl.haxx.se/) jest używany do testowania.

## <a name="migrate-from-net-framework"></a>Migrowanie z programu .NET Framework

Aby uzyskać przewodnik po migracji ASP.NET aplikacji 4.x do ASP.NET Core, zobacz <xref:migration/proper-to-2x/index>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core to wieloplatformowa, wydajna struktura [open source](https://github.com/dotnet/aspnetcore) do tworzenia nowoczesnych aplikacji połączonych z Chmurą i połączonych z Internetem. Platforma ASP.NET Core umożliwia:

* Tworzenie aplikacji i usług internetowych, aplikacji [Internetu rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i zaplecza mobilnego.
* Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.
* Wdrażanie w chmurze lub lokalnie.
* Uruchamianie na platformie [.NET Core lub .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Dlaczego warto wybrać ASP.NET Core?

Miliony programistów używają lub używają [ASP.NET 4.x](/aspnet/overview) do tworzenia aplikacji internetowych. ASP.NET Core to przeprojektowana platforma ASP.NET 4.x, w której wprowadzono zmiany architektoniczne w celu stworzenia bardziej zwartej i modułowej struktury.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core

Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):

* Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.
* [Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.
* [Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).
* [Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.
* Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.
* [Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.
* [Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.

## <a name="client-side-development"></a>Programowanie po stronie klienta

ASP.NET Core bezproblemowo integruje się z popularnymi strukturami i bibliotekami po stronie klienta, w tym [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)i [Bootstrap.](https://getbootstrap.com/) Aby uzyskać więcej <xref:blazor/index> informacji, zobacz i tematy pokrewne w obszarze *Rozwój po stronie klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>Platforma ASP.NET Core ukierunkowana na platformę .NET Framework

Platforma ASP.NET Core 2.x może jako cel mieć platformę .NET Core lub .NET Framework. Aplikacje platformy ASP.NET Core ukierunkowane na platformę .NET Framework nie są wieloplatformowe &mdash; działają tylko w systemie Windows. Ogólnie rzecz biorąc, platforma ASP.NET Core 2.x jest zbudowana z bibliotek [.NET Standard](/dotnet/standard/net-standard). Biblioteki napisane za pomocą platformy .NET Standard 2.0 są uruchamiane na dowolnej [platformie .NET, która implementuje program .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2.x jest obsługiwany w wersjach programu .NET Framework implementuj .NET Standard 2.0:

* Zalecana jest najnowsza wersja programu .NET Framework.
* Platforma .NET Framework 4.6.1 lub nowsza.

Platforma ASP.NET Core 3.0 i nowsze wersje będą działać tylko na platformie .NET Core. Aby uzyskać więcej informacji o tej zmianie, zobacz [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Pierwsze spojrzenie na zmiany wprowadzane na platformie ASP.NET Core 3.0).

Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem. Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:

* Wieloplatformowość. Działa w systemach macOS, Linux i Windows.
* Większa wydajność
* [Przechowywanie wersji obok siebie](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* Nowe interfejsy API
* Kod open source

Ciężko pracujemy nad zlikwidowaniem rozbieżności między interfejsami API platform .NET Framework i .NET Core. Pakiet [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) udostępnił tysiące interfejsów API specyficznych dla systemu Windows na platformie .NET Core. Te interfejsy API nie były dostępne na platformie .NET Core 1.x.

## <a name="recommended-learning-path"></a>Zalecana ścieżka szkoleniowa

Przy rozpoczynaniu programowania aplikacji platformy ASP.NET Core zalecamy następujący zestaw samouczków i artykułów:

1. Postępuj zgodnie z samouczkiem typu aplikacji, którą chcesz opracować lub obsługiwać.

   |Typ aplikacji  |Scenariusz  |Samouczek  |
   |----------|----------|----------|
   |Aplikacja internetowa                   | Programowanie od nowa        |[Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplikacja internetowa                   | Konserwacja aplikacji MVC |[Wprowadzenie do wzorca MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Interfejs API sieci Web                   |                            |[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger; |
   |Aplikacja czasu rzeczywistego             |                            |[Wprowadzenie do usługi SignalR](xref:tutorials/signalr) |

1. Postępuj zgodnie z samouczkiem, który pokazuje, jak zrobić podstawowy dostęp do danych.

   |Scenariusz  |Samouczek  |
   |----------|----------|
   | Programowanie od nowa        |[Platforma Razor Pages z platformą Entity Framework Core](xref:data/ef-rp/intro) |
   | Konserwacja aplikacji MVC |[Wzorzec MVC z platformą Entity Framework Core](xref:data/ef-mvc/intro) |

1. Przeczytaj omówienie ASP.NET [podstawowe podstawy,](xref:fundamentals/index) które mają zastosowanie do wszystkich typów aplikacji.

1. Przeglądaj spis treści, aby znaleźć inne interesujące tematy.

&dagger;Istnieje również [samouczek interfejsu API sieci web, który można wykonać całkowicie w przeglądarce,](/learn/modules/build-web-api-net-core)nie jest wymagana instalacja lokalnego IDE. Kod jest wykonywany w usłudze [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), a do testowania służy narzędzie [curl](https://curl.haxx.se/).

## <a name="migrate-from-net-framework"></a>Migrowanie z programu .NET Framework

Aby uzyskać przewodnik po migracji ASP.NET aplikacji do ASP.NET Core, <xref:migration/proper-to-2x/index>zobacz .

::: moniker-end

## <a name="how-to-download-a-sample"></a>Instrukcje: pobieranie pliku

Wiele artykułów i samouczków zawiera linki do kodu przykładowego.

1. [Pobierz plik zip repozytorium ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Rozpakuj plik *Docs-master.zip*.
1. Adres URL linku do przykładu pomoże Ci przejść do katalogu przykładu.

### <a name="preprocessor-directives-in-sample-code"></a>Dyrektywy preprocesora w przykładowym kodzie

Aby zademonstrować wiele scenariuszy, `#define` `#if-#else/#elif-#endif` przykładowe aplikacje używają dyrektyw i preprocesora do selektywnego kompilowania i uruchamiania różnych sekcji przykładowego kodu. Dla tych przykładów, które korzystają z `#define` tego podejścia, ustaw dyrektywę w górnej części plików Języka C#, aby zdefiniować symbol skojarzony ze scenariuszem, który chcesz uruchomić. Niektóre przykłady wymagają zdefiniowania symbolu u góry wielu plików w celu uruchomienia scenariusza.

Na przykład następująca lista symboli `#define` wskazuje, że są dostępne cztery scenariusze (jeden scenariusz na symbol). Aktualna konfiguracja przykładu powoduje uruchomienie scenariusza `TemplateCode`:

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Aby zmienić przykład w celu uruchomienia scenariusza `ExpandDefault`, zdefiniuj symbol `ExpandDefault` i pozostaw pozostałe symbole jako przekształcone w komentarz:

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Więcej informacji na temat używania [ dyrektyw preprocesora języka C#](/dotnet/csharp/language-reference/preprocessor-directives/) do selektywnego kompilowania sekcji kodu można znaleźć w tematach [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) (#define (odwołanie w języku C#)) i [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) (#if (odwołanie w języku C#)).

### <a name="regions-in-sample-code"></a>Regiony w przykładowym kodzie

Niektóre przykładowe aplikacje zawierają sekcje kodu w otoczeniu [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) i [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) dyrektyw C#. System tworzenia dokumentacji wstawia te regiony do renderowanych tematów dokumentacji.  

Nazwy regionów zwykle zawierają wyraz „snippet”. W poniższym przykładzie pokazano region o nazwie `snippet_WebHostDefaults`:

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

Wcześniejszy fragment kodu w języku C# jest przywoływany w pliku markdown tematu za pomocą następującego wiersza:

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Można bezpiecznie zignorować (lub `#region` `#endregion` usunąć) i dyrektyw, które otaczają kod. Nie należy zmieniać kodu w ramach tych dyrektyw, jeśli planujesz uruchomić przykładowe scenariusze opisane w temacie. Kod możesz swobodnie modyfikować, eksperymentując z innymi scenariuszami.

Aby uzyskać więcej informacji, zobacz [Współtworzenie dokumentacji platformy ASP.NET: fragmenty kodu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Platforma ASP.NET Core — podstawy](xref:fundamentals/index)
* [Cotygodniowe podsumowanie ASP.NET Community Standup](https://live.asp.net/) zawiera aktualności o postępach i planach zespołu. Zawiera też informacje o polecanych blogach i oprogramowaniu innych firm.
