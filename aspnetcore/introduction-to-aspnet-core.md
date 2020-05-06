---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Zapoznaj się z wprowadzeniem do ASP.NET Core, międzyplatformowej platformy typu open source do tworzenia nowoczesnych aplikacji z włączoną obsługą chmury.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 7f46051193681ecac59428b77ca1e36830c7bb63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776336"
---
# <a name="introduction-to-aspnet-core"></a>Wprowadzenie do platformy ASP.NET Core

[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) i [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core to międzyplatformowa platforma typu ["Open Source](https://github.com/dotnet/aspnetcore) ", która umożliwia tworzenie nowoczesnych aplikacji z obsługą chmury. Platforma ASP.NET Core umożliwia:

* Twórz aplikacje sieci Web i usługi, aplikacje [Internet rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i frontony mobilne.
* Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.
* Wdrażanie w chmurze lub lokalnie.
* Uruchamiany na [platformie .NET Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Dlaczego warto wybrać ASP.NET Core?

Miliony deweloperów używają lub używały [ASP.NET 4. x](/aspnet/overview) do tworzenia aplikacji sieci Web. ASP.NET Core to projekt ASP.NET 4. x, w tym zmiany architektury, które powodują powstanie produkcji oszczędnej i większej struktury modularnej.

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

ASP.NET Core zapewnia bezproblemową integrację z popularnymi strukturami i bibliotekami po stronie klienta, takimi jak [Blazor](xref:blazor/index), [kątowy](xref:spa/angular), [reagowanie](xref:spa/react)i [Bootstrap](https://getbootstrap.com/). Aby uzyskać więcej informacji, <xref:blazor/index> Zobacz i Tematy pokrewne w obszarze *programowanie po stronie klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core platform docelowych

ASP.NET Core 3. x i nowsze mogą dotyczyć tylko .NET Core. Ogólnie rzecz biorąc ASP.NET Core składa się z bibliotek [.NET Standard](/dotnet/standard/net-standard) . Biblioteki z .NET Standard 2,0 są uruchamiane na dowolnej [platformie .NET implementującej .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).

Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem. Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:

* Wieloplatformowość. Działa w systemach Windows, macOS i Linux.
* Większa wydajność
* [Przechowywanie wersji obok siebie](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nowe interfejsy API
* Kod open source

## <a name="recommended-learning-path"></a>Zalecana ścieżka szkoleniowa

Zalecamy poniższą sekwencję samouczków, aby zapoznać się z wprowadzeniem do tworzenia aplikacji ASP.NET Core:

1. Postępuj zgodnie z samouczkiem dotyczącym typu aplikacji, który chcesz opracowywać lub obsłudze.

   |Typ aplikacji  |Scenariusz  |Samouczek  |
   |----------|----------|----------|
   |Aplikacja internetowa                   | Tworzenie nowego interfejsu użytkownika sieci Web po stronie serwera |[Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplikacja internetowa                   | Obsługa aplikacji MVC |[Wprowadzenie do wzorca MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Aplikacja internetowa                   | Programowanie interfejsu użytkownika sieci Web po stronie klienta |[Wprowadzenie do Blazor](xref:tutorials/first-blazor-app) |
   |Interfejs API sieci Web                   | RESTful usługi HTTP |[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger; |
   |Aplikacja zdalnego wywołania procedury | Usługi pierwszego kontraktu przy użyciu buforów protokołu |[Wprowadzenie do usługi gRPC](xref:tutorials/grpc/grpc-start) |
   |Aplikacja czasu rzeczywistego             | Dwukierunkowa komunikacja między serwerami i połączonymi klientami |[Wprowadzenie do usługi SignalR](xref:tutorials/signalr) |

1. Postępuj zgodnie z samouczkiem, który pokazuje, jak przeprowadzić podstawowy dostęp do danych.

   |Scenariusz  |Samouczek  |
   |----------|----------|
   |Nowe programowanie        |[Platforma Razor Pages z platformą Entity Framework Core](xref:data/ef-rp/intro) |
   |Obsługa aplikacji MVC |[Wzorzec MVC z platformą Entity Framework Core](xref:data/ef-mvc/intro) |

1. Zapoznaj się z omówieniem ASP.NET Core [podstawowych](xref:fundamentals/index) , które mają zastosowanie do wszystkich typów aplikacji.

1. Przejrzyj Spis treści pod kątem innych tematów zainteresowania.

&dagger;Dostępny jest również [Interaktywny samouczek internetowy interfejsu API](/learn/modules/build-web-api-net-core). Nie jest wymagana instalacja lokalna narzędzi programistycznych. Kod jest uruchamiany w [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w przeglądarce, a [zwinięcie](https://curl.haxx.se/) służy do testowania.

## <a name="migrate-from-net-framework"></a>Migrowanie z .NET Framework

Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET 4. x do ASP.NET Core można <xref:migration/proper-to-2x/index>znaleźć w temacie.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core to międzyplatformowa platforma typu ["Open Source](https://github.com/dotnet/aspnetcore) ", która umożliwia tworzenie nowoczesnych aplikacji z obsługą chmury. Platforma ASP.NET Core umożliwia:

* Twórz aplikacje sieci Web i usługi, aplikacje [Internet rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i frontony mobilne.
* Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.
* Wdrażanie w chmurze lub lokalnie.
* Uruchamianie na platformie [.NET Core lub .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Dlaczego warto wybrać ASP.NET Core?

Miliony deweloperów używają lub używały [ASP.NET 4. x](/aspnet/overview) do tworzenia aplikacji sieci Web. ASP.NET Core to przeprojektowana platforma ASP.NET 4.x, w której wprowadzono zmiany architektoniczne w celu stworzenia bardziej zwartej i modułowej struktury.

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

ASP.NET Core zapewnia bezproblemową integrację z popularnymi strukturami i bibliotekami po stronie klienta, takimi jak [Blazor](xref:blazor/index), [kątowy](xref:spa/angular), [reagowanie](xref:spa/react)i [Bootstrap](https://getbootstrap.com/). Aby uzyskać więcej informacji, <xref:blazor/index> Zobacz i Tematy pokrewne w obszarze *programowanie po stronie klienta*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>Platforma ASP.NET Core ukierunkowana na platformę .NET Framework

Platforma ASP.NET Core 2.x może jako cel mieć platformę .NET Core lub .NET Framework. Aplikacje platformy ASP.NET Core ukierunkowane na platformę .NET Framework nie są wieloplatformowe &mdash; działają tylko w systemie Windows. Ogólnie rzecz biorąc, platforma ASP.NET Core 2.x jest zbudowana z bibliotek [.NET Standard](/dotnet/standard/net-standard). Biblioteki z .NET Standard 2,0 są uruchamiane na dowolnej [platformie .NET implementującej .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2. x jest obsługiwana w wersjach .NET Framework, które implementują .NET Standard 2,0:

* Zaleca się .NET Framework najnowszej wersji.
* Platforma .NET Framework 4.6.1 lub nowsza.

Platforma ASP.NET Core 3.0 i nowsze wersje będą działać tylko na platformie .NET Core. Aby uzyskać więcej informacji o tej zmianie, zobacz [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Pierwsze spojrzenie na zmiany wprowadzane na platformie ASP.NET Core 3.0).

Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem. Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:

* Wieloplatformowość. Działa w systemach macOS, Linux i Windows.
* Większa wydajność
* [Przechowywanie wersji obok siebie](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nowe interfejsy API
* Kod open source

Aby pomóc w zamknięciu przerwy w interfejsie API z .NET Framework do programu .NET Core, [pakiet zgodności systemu Windows](/dotnet/core/porting/windows-compat-pack) wprowadził tysiące interfejsów API tylko dla systemu Windows dostępnych w programie .NET Core. Te interfejsy API nie były dostępne na platformie .NET Core 1.x.

## <a name="recommended-learning-path"></a>Zalecana ścieżka szkoleniowa

Przy rozpoczynaniu programowania aplikacji platformy ASP.NET Core zalecamy następujący zestaw samouczków i artykułów:

1. Postępuj zgodnie z samouczkiem dotyczącym typu aplikacji, którą chcesz opracowywać lub obsłudze.

   |Typ aplikacji  |Scenariusz  |Samouczek  |
   |----------|----------|----------|
   |Aplikacja internetowa                   | Programowanie od nowa        |[Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start) |
   |Aplikacja internetowa                   | Konserwacja aplikacji MVC |[Wprowadzenie do wzorca MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Interfejs API sieci Web                   |                            |[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger; |
   |Aplikacja czasu rzeczywistego             |                            |[Wprowadzenie do usługi SignalR](xref:tutorials/signalr) |

1. Postępuj zgodnie z samouczkiem, który pokazuje, jak przeprowadzić podstawowy dostęp do danych.

   |Scenariusz  |Samouczek  |
   |----------|----------|
   | Programowanie od nowa        |[RazorStrony z Entity Framework Core](xref:data/ef-rp/intro) |
   | Konserwacja aplikacji MVC |[Wzorzec MVC z platformą Entity Framework Core](xref:data/ef-mvc/intro) |

1. Zapoznaj się z omówieniem ASP.NET Core [podstawowych](xref:fundamentals/index) , które mają zastosowanie do wszystkich typów aplikacji.

1. Przeglądaj spis treści, aby znaleźć inne interesujące tematy.

&dagger;Dostępny jest również [samouczek interfejsu API sieci Web, który należy wykonać w całości w przeglądarce](/learn/modules/build-web-api-net-core), nie jest wymagana żadna lokalna instalacja środowiska IDE. Kod jest wykonywany w usłudze [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), a do testowania służy narzędzie [curl](https://curl.haxx.se/).

## <a name="migrate-from-net-framework"></a>Migrowanie z .NET Framework

Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET do ASP.NET Core można <xref:migration/proper-to-2x/index>znaleźć w temacie.

::: moniker-end

## <a name="how-to-download-a-sample"></a>Instrukcje: pobieranie pliku

Wiele artykułów i samouczków zawiera linki do kodu przykładowego.

1. [Pobierz plik zip repozytorium ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).
1. Rozpakuj plik *Docs-master.zip*.
1. Adres URL linku do przykładu pomoże Ci przejść do katalogu przykładu.

### <a name="preprocessor-directives-in-sample-code"></a>Dyrektywy preprocesora w przykładowym kodzie

Aby przedstawić wiele scenariuszy, przykładowe aplikacje wykorzystują `#define` dyrektywy `#if-#else/#elif-#endif` i preprocesora, aby wybiórczo kompilować i uruchamiać różne sekcje przykładowego kodu. Dla tych przykładów, które korzystają z tego podejścia, należy ustawić `#define` dyrektywę w górnej części plików C#, aby zdefiniować symbol skojarzony z scenariuszem, który chcesz uruchomić. Niektóre przykłady wymagają zdefiniowania symbolu w górnej części wielu plików, aby można było uruchomić scenariusz.

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

Niektóre przykładowe aplikacje zawierają sekcje kodu otoczone dyrektywami [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) i [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C#. System tworzenia dokumentacji wstawia te regiony do renderowanych tematów dokumentacji.  

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

Licencjobiorca może bezpiecznie zignorować (lub usunąć) `#region` dyrektywy `#endregion` i, które są otaczające kod. Nie zmieniaj kodu w ramach tych dyrektyw, jeśli planujesz uruchamiać przykładowe scenariusze opisane w temacie. Kod możesz swobodnie modyfikować, eksperymentując z innymi scenariuszami.

Aby uzyskać więcej informacji, zobacz [Współtworzenie dokumentacji platformy ASP.NET: fragmenty kodu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Platforma ASP.NET Core — podstawy](xref:fundamentals/index)
* [Cotygodniowe podsumowanie ASP.NET Community Standup](https://live.asp.net/) zawiera aktualności o postępach i planach zespołu. Zawiera też informacje o polecanych blogach i oprogramowaniu innych firm.
