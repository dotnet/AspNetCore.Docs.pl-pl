---
title: Co nowego w ASP.NET Core 2.0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667259"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co nowego w ASP.NET Core 2.0

W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 2.0, z łączami do odpowiedniej dokumentacji.

## <a name="razor-pages"></a>Razor Pages

Razor Pages to nowa funkcja ASP.NET Core MVC, która sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne.

Aby uzyskać więcej informacji, zobacz wprowadzenie i samouczek:

* [Wprowadzenie do produktu Razor Pages](xref:razor-pages/index)
* [Wprowadzenie do korzystania ze stron Razor](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Rdzenie metapakiet

Nowy ASP.NET Core zawiera wszystkie pakiety wykonane i obsługiwane przez zespoły ASP.NET core core i entity framework core, wraz z ich wewnętrznych i zewnętrznych zależności. Nie musisz już wybierać poszczególnych funkcji ASP.NET Core według pakietu. Wszystkie funkcje są zawarte w pakiecie [Microsoft.AspNetCore.All.](https://www.nuget.org/packages/Microsoft.AspNetCore.All) Szablony domyślne używają tego pakietu.

Aby uzyskać więcej informacji, zobacz [Microsoft.AspNetCore.All metapackage dla ASP.NET Core 2.0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Aplikacje korzystające `Microsoft.AspNetCore.All` z metapakiety automatycznie korzystają z nowego magazynu wykonywania .NET Core. Sklep zawiera wszystkie zasoby środowiska wykonawczego potrzebne do uruchamiania aplikacji ASP.NET Core 2.0. Korzystając z `Microsoft.AspNetCore.All` metapakietu, żadne zasoby z pakietów Core NuGet, do których odwołuje się odwołuje się ASP.NET Core NuGet, nie są wdrażane z aplikacją, ponieważ znajdują się one już w systemie docelowym. Zasoby w Magazynie Środowiska wykonawczego są również wstępnie kompilowane w celu skrócenia czasu uruchamiania aplikacji.

Aby uzyskać więcej informacji, zobacz [Magazyn środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2.0

ASP.NET core 2.0 docelowy pakietów .NET Standard 2.0. Do pakietów mogą odwoływać się inne biblioteki .NET Standard 2.0 i mogą być uruchamiane w implementacjach .NET Standard 2.0 .NET, w tym .NET Core 2.0 i .NET Framework 4.6.1. 

Metapakiet `Microsoft.AspNetCore.All` jest przeznaczony tylko dla platformy .NET Core 2.0, ponieważ jest przeznaczony do użycia w magazynie wykonywania .NET Core 2.0.

## <a name="configuration-update"></a>Aktualizacja konfiguracji

Wystąpienie `IConfiguration` jest domyślnie dodawane do kontenera usług w ASP.NET Core 2.0. `IConfiguration`w kontenerze usług ułatwia aplikacjom pobieranie wartości konfiguracji z kontenera.

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Aktualizacja rejestrowania

W ASP.NET Core 2.0 rejestrowanie jest domyślnie włączone do systemu iniekcji zależności (DI). Dodaj dostawców i konfigurujesz filtrowanie w pliku *Program.cs,* a nie w pliku *Startup.cs.* Wartość domyślna `ILoggerFactory` obsługuje filtrowanie w sposób umożliwiający korzystanie z jednego elastycznego podejścia zarówno do filtrowania między dostawcami, jak i filtrowania określonego dostawcy.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do rejestrowania](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizacja uwierzytelniania

Nowy model uwierzytelniania ułatwia konfigurowanie uwierzytelniania dla aplikacji przy użyciu DI.

Dostępne są nowe szablony do konfigurowania uwierzytelniania aplikacji sieci Web i internetowych interfejsów API przy użyciu [usługi Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Aktualizacja tożsamości

Ułatwiliśmy tworzenie bezpiecznych interfejsów API sieci Web przy użyciu tożsamości w ASP.NET Core 2.0. Tokeny dostępu można uzyskać w celu uzyskania dostępu do internetowych interfejsów API przy użyciu [biblioteki uwierzytelniania firmy Microsoft (MSAL).](https://www.nuget.org/packages/Microsoft.Identity.Client)

Aby uzyskać więcej informacji na temat zmian uwierzytelniania w 2.0, zobacz następujące zasoby:

* [Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core](xref:security/authentication/accconfirm)
* [Włącz generowanie kodu QR dla aplikacji uwierzytelniającego w ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrowanie uwierzytelniania i tożsamości w celu ASP.NET rdzenia 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Szablony SPA

Dostępne są szablony projektów aplikacji jednostronicowej (SPA) dla angular, Aurelia, Knockout.js, React.js i React.js z Redux. Szablon kątowy został zaktualizowany do Angular 4. Szablony Angular i React są domyślnie dostępne; aby uzyskać informacje o tym, jak uzyskać inne szablony, zobacz [Tworzenie nowego projektu SPA](xref:client-side/spa-services#create-a-new-project). Aby uzyskać informacje na temat tworzenia SPA w <xref:client-side/spa-services>ASP.NET Core, zobacz .

## <a name="kestrel-improvements"></a>Ulepszenia pustułki

Serwer internetowy Kestrel ma nowe funkcje, które sprawiają, że jest bardziej odpowiedni jako serwer internetowy. W nowej `KestrelServerOptions` `Limits` właściwości klasy dodano wiele opcji konfiguracji ograniczeń serwera. Dodaj limity dla następujących elementów:

* Maksymalna liczba połączeń klientów
* Maksymalny rozmiar treści żądania
* Minimalna szybkość danych treści żądania

Aby uzyskać więcej informacji, zobacz [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Nazwa WebListener na HTTP.sys

Pakiety `Microsoft.AspNetCore.Server.WebListener` `Microsoft.Net.Http.Server` i zostały połączone w `Microsoft.AspNetCore.Server.HttpSys`nowy pakiet . Przestrzenie nazw zostały zaktualizowane, aby były zgodne.

Aby uzyskać więcej informacji, zobacz [HTTP.sys implementacji serwera sieci web w ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Ulepszona obsługa nagłówka HTTP

Podczas korzystania z MVC `FileStreamResult` `FileContentResult`do przesyłania lub , masz `ETag` teraz `LastModified` możliwość ustawienia lub daty na treści, które przesyłane. Można ustawić te wartości na zwróconej zawartości z kodem podobnym do następującego:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Plik zwrócony odwiedzającym ma odpowiednie nagłówki `ETag` HTTP `LastModified` dla i wartości.

Jeśli użytkownik aplikacji żąda zawartości z nagłówkiem żądanie zakresu, ASP.NET Core rozpoznaje żądanie i obsługuje nagłówek. Jeśli żądana zawartość może być częściowo dostarczona, ASP.NET Core odpowiednio pomija i zwraca tylko żądany zestaw bajtów. Nie trzeba zapisywać żadnych specjalnych programów obsługi do metod, aby dostosować lub obsługiwać tę funkcję; jest automatycznie obsługiwany za Ciebie.

## <a name="hosting-startup-and-application-insights"></a>Hostowanie uruchamiania i usługi Application Insights

Środowiska hostingowe można teraz wstrzyknąć dodatkowe zależności pakietów i wykonać kod podczas uruchamiania aplikacji, bez konieczności aplikacji jawnie wziąć zależność lub wywołać żadnych metod. Ta funkcja może służyć do umożliwienia niektórych środowisk do "light-up" funkcje unikatowe dla tego środowiska bez aplikacji konieczności wiedzieć z wyprzedzeniem. 

W ASP.NET Core 2.0 ta funkcja jest używana do automatycznego włączania diagnostyki usługi Application Insights podczas debugowania w programie Visual Studio i (po włączeniu) podczas uruchamiania w usłudze Azure App Services. W rezultacie szablony projektu nie są już domyślnie dodawanymi pakietami i kodem usługi Application Insights.

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatyczne używanie tokenów zapobiegawczych fałszerskości

ASP.NET Core zawsze pomagał zawartości kodowania HTML domyślnie, ale z nową wersją krok jest podejmowany, aby zapobiec atakom xsrf (cross-site request fałszerza). ASP.NET Core będzie teraz domyślnie emitować tokeny antyzwiązania z fałszerstwem i sprawdzać ich poprawność w akcjach i stronach post formularza bez dodatkowej konfiguracji.

Aby uzyskać więcej informacji, zobacz [Zapobieganie atakom xsrf/CSRF (Cross-Site Request Fałszerstwa" ( XSRF/CSRF) (Zapobieganie atakom wielomiejscowym).](xref:security/anti-request-forgery)

## <a name="automatic-precompilation"></a>Automatyczna wstępna kompilacja

Razor view pre-compilation jest domyślnie włączona podczas publikowania, co zmniejsza rozmiar wydruku i czas uruchamiania aplikacji.

Aby uzyskać więcej informacji, zobacz [Kompilacja i wstępna kompilacja widoku Razor w ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>Obsługa maszynki do golenia dla języka C# 7.1

Aparat widoku Razor został zaktualizowany do pracy z nowym kompilatorem Roslyn. Obejmuje to obsługę funkcji języka C# 7.1, takich jak domyślne wyrażenia, wywnioskowane nazwy krotek i dopasowywanie wzorców z rodzajami ogólnymi. Aby użyć języka C# 7.1 w projekcie, dodaj następującą właściwość w pliku projektu, a następnie ponownie załaduj rozwiązanie:

```xml
<LangVersion>latest</LangVersion>
```

Aby uzyskać informacje o stanie funkcji języka C# 7.1, zobacz [repozytorium Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Inne aktualizacje dokumentacji dla 2.0

* [Profile publikowania w programie Visual Studio dla ASP.NET wdrożenia aplikacji Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Zarządzanie kluczami](xref:security/data-protection/implementation/key-management)
* [Konfigurowanie uwierzytelniania na Facebooku](xref:security/authentication/facebook-logins)
* [Konfigurowanie uwierzytelniania twitterowego](xref:security/authentication/twitter-logins)
* [Konfigurowanie uwierzytelniania Google](xref:security/authentication/google-logins)
* [Konfigurowanie uwierzytelniania konta Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Wskazówki dotyczące migracji

Aby uzyskać wskazówki dotyczące migracji aplikacji ASP.NET Core 1.x do ASP.NET Core 2.0, zobacz następujące zasoby:

* [Migracja z ASP.NET Core 1.x do ASP.NET Core 2.0](xref:migration/1x-to-2x/index)
* [Migrowanie uwierzytelniania i tożsamości w celu ASP.NET rdzenia 2.0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zobacz [ASP.NET Informacje o wersji Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Aby połączyć się z postępami i planami zespołu programistów ASP.NET Core, dostroić się do [ASP.NET Standup społeczności](https://live.asp.net/).
