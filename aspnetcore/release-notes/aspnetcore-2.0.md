---
title: Co nowego w ASP.NET Core 2,0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.0
ms.openlocfilehash: b1fc486633ab7c7d7bee4f8981a978747b2890da
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775820"
---
# <a name="whats-new-in-aspnet-core-20"></a>Co nowego w ASP.NET Core 2,0

W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 2,0 z linkami do odpowiedniej dokumentacji.

## <a name="razor-pages"></a>RazorPage

RazorStrony są nową funkcją ASP.NET Core MVC, która sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.

Aby uzyskać więcej informacji, zobacz Wprowadzenie i samouczek:

* [Wprowadzenie do Razor stron](xref:razor-pages/index)
* [Wprowadzenie do Razor stron](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a>ASP.NET Core, pakiet

Nowy ASP.NET Core pakietem zawierającym wszystkie pakiety wykonane i obsługiwane przez zespoły ASP.NET Core i Entity Framework Core wraz z ich wewnętrznymi i zewnętrznymi zależnościami. Nie trzeba już wybierać poszczególnych funkcji ASP.NET Core według pakietu. Wszystkie funkcje są zawarte w pakiecie [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) . Szablony domyślne używają tego pakietu.

Aby uzyskać więcej informacji, zobacz [Microsoft. AspNetCore. All pakietu dla ASP.NET Core 2,0](xref:fundamentals/metapackage).

## <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Aplikacje korzystające z `Microsoft.AspNetCore.All` pakietu metadanych automatycznie wykorzystują nowy magazyn środowiska uruchomieniowego platformy .NET Core. Magazyn zawiera wszystkie zasoby środowiska uruchomieniowego potrzebne do uruchamiania aplikacji ASP.NET Core 2,0. W przypadku korzystania `Microsoft.AspNetCore.All` z pakietubinding nie są wdrażane żadne zasoby z przywoływanych ASP.NET Core pakietów NuGet z aplikacją, ponieważ znajdują się one już w systemie docelowym. Elementy zawartości w magazynie środowiska uruchomieniowego są również wstępnie skompilowane w celu poprawienia czasu uruchamiania aplikacji.

Aby uzyskać więcej informacji, zobacz [Magazyn środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

## <a name="net-standard-20"></a>.NET Standard 2,0

.NET Standard 2,0 pakietów ASP.NET Core 2,0. Do pakietów mogą odwoływać się inne .NET Standard biblioteki 2,0, które mogą być uruchamiane na .NET Standard 2,0 zgodnych implementacjach platformy .NET, w tym .NET Core 2,0 i .NET Framework 4.6.1. 

`Microsoft.AspNetCore.All` Pakietem docelowym jest tylko .net Core 2,0, ponieważ jest on przeznaczony do użycia z magazynem środowiska uruchomieniowego programu .net Core 2,0.

## <a name="configuration-update"></a>Aktualizacja konfiguracji

`IConfiguration` Wystąpienie jest domyślnie dodawane do kontenera usługi w ASP.NET Core 2,0. `IConfiguration`w kontenerze usługi Usługa ułatwia aplikacjom pobieranie wartości konfiguracyjnych z kontenera.

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).

## <a name="logging-update"></a>Aktualizacja rejestrowania

W ASP.NET Core 2,0 rejestrowanie jest domyślnie włączone do systemu iniekcji zależności. Należy dodać dostawców i skonfigurować filtrowanie w pliku *program.cs* , a nie w pliku *Startup.cs* . Domyślnie `ILoggerFactory` obsługuje filtrowanie w taki sposób, który pozwala korzystać z jednej elastycznej metody filtrowania między dostawcami i filtrowania określonego dostawcy.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowania](xref:fundamentals/logging/index).

## <a name="authentication-update"></a>Aktualizacja uwierzytelniania

Nowy model uwierzytelniania ułatwia konfigurowanie uwierzytelniania aplikacji przy użyciu funkcji DI.

Nowe szablony są dostępne do konfigurowania uwierzytelniania dla aplikacji sieci Web i interfejsów API sieci Web przy użyciu [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).

## <a name="identity-update"></a>Identityaktualizacji

Ułatwiamy tworzenie bezpiecznych interfejsów API sieci Web za pomocą Identity programu w ASP.NET Core 2,0. Możesz uzyskać tokeny dostępu do uzyskiwania dostępu do interfejsów API sieci Web przy użyciu [biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).

Aby uzyskać więcej informacji na temat zmian uwierzytelniania w 2,0, zobacz następujące zasoby:

* [Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core](xref:security/authentication/accconfirm)
* [Włącz generowanie kodu QR dla aplikacji uwierzytelniania w ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Migrowanie uwierzytelniania Identity i do ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a>Szablony SPA

Dostępne są szablony projektów aplikacji jednostronicowych (SPA) dla kątowych, Aurelia, odcinania. js, replika. js i reaguje. js z Redux. Szablon kątowy został zaktualizowany do kąta 4. Szablony kątowe i reagowanie są domyślnie dostępne; Aby uzyskać informacje o sposobach uzyskiwania innych szablonów, zobacz [Tworzenie nowego projektu Spa](xref:client-side/spa-services#create-a-new-project). Aby uzyskać informacje na temat sposobu tworzenia SPA w ASP.NET Core, zobacz <xref:client-side/spa-services>.

## <a name="kestrel-improvements"></a>Udoskonalenia Kestrel

Serwer sieci Web Kestrel ma nowe funkcje, które sprawiają, że jest to bardziej odpowiednie jako serwer dostępny z Internetu. W nowej `KestrelServerOptions` `Limits` właściwości klasy są dodawane różne opcje konfiguracji ograniczeń serwera. Dodaj limity dla następujących:

* Maksymalna liczba połączeń klienta
* Maksymalny rozmiar treści żądania
* Minimalna stawka danych treści żądania

Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel w ASP.NET Core](xref:fundamentals/servers/kestrel).

## <a name="weblistener-renamed-to-httpsys"></a>Nazwa elementu webListener została zmieniona na HTTP. sys

Pakiety `Microsoft.AspNetCore.Server.WebListener` i `Microsoft.Net.Http.Server` zostały scalone z nowym pakietem `Microsoft.AspNetCore.Server.HttpSys`. Przestrzenie nazw zostały zaktualizowane w celu dopasowania.

Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web http. sys w ASP.NET Core](xref:fundamentals/servers/httpsys).

## <a name="enhanced-http-header-support"></a>Ulepszona obsługa nagłówka HTTP

`FileStreamResult` W przypadku przesyłania lub `FileContentResult`do programu przy użyciu MVC jest teraz dostępna opcja ustawiania `ETag` lub `LastModified` daty przesyłanej zawartości. Można ustawić te wartości w zwracanej zawartości z kodem podobnym do poniższego:

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

Plik zwrócony do odwiedzających ma odpowiednie nagłówki HTTP dla wartości `ETag` i. `LastModified`

Jeśli osoba odwiedzająca aplikację żąda zawartości z nagłówkiem żądania zakresu, ASP.NET Core rozpoznaje żądanie i obsługuje nagłówek. Jeśli żądana zawartość może zostać dostarczona częściowo, ASP.NET Core odpowiednio pomija i zwróci tylko żądany zestaw bajtów. Nie musisz pisać żadnych specjalnych programów obsługi w swoich metodach, aby dostosować lub obsłużyć tę funkcję. jest ono automatycznie obsługiwane.

## <a name="hosting-startup-and-application-insights"></a>Hosting uruchamiania i Application Insights

Środowiska hostingu mogą teraz dodawać dodatkowe zależności pakietu i wykonywać kod podczas uruchamiania aplikacji, bez konieczności jawnego podjęcia zależności przez aplikację lub wywołania jakichkolwiek metod. Ta funkcja może służyć do włączania pewnych środowisk jako "świateł-up", które są unikatowe dla danego środowiska, bez konieczności poznania się przed czasem. 

W ASP.NET Core 2,0 Ta funkcja jest używana do automatycznego włączania diagnostyki Application Insights podczas debugowania w programie Visual Studio i (po dodaniu go w programie) podczas uruchamiania na platformie Azure App Services. W związku z tym szablony projektu nie będą już domyślnie dodawać Application Insights pakietów i kodu.

Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).

## <a name="automatic-use-of-anti-forgery-tokens"></a>Automatyczne korzystanie z tokenów chroniących przed fałszerstwem

ASP.NET Core zawsze pomogła korzystać z zawartości kodowanej w języku HTML domyślnie, ale z nową wersją jest podejmowana dodatkowa czynność, która pomaga zapobiegać atakom przez wiele witryn (XSRF). ASP.NET Core teraz emitują tokeny zabezpieczające przed fałszerstwem domyślnie i sprawdzają poprawność przy użyciu akcji POST formularzy i stron bez dodatkowej konfiguracji.

Aby uzyskać więcej informacji, zobacz [Zapobiegaj fałszowaniu żądań między witrynami (XSRF/CSRF)](xref:security/anti-request-forgery).

## <a name="automatic-precompilation"></a>Automatyczna kompilacja prekompilowana

RazorWidok sprzed kompilacji jest domyślnie włączony podczas publikowania, zmniejszając rozmiar danych wyjściowych publikowania i czas uruchamiania aplikacji.

Aby uzyskać więcej informacji, [ Razor Zobacz Przeglądanie kompilacji i wstępnej kompilacji w ASP.NET Core](xref:mvc/views/view-compilation).

## <a name="razor-support-for-c-71"></a>RazorObsługa języka C# 7,1

Aparat Razor widoku został zaktualizowany, aby współpracował z nowym kompilatorem Roslyn. Obejmuje to obsługę funkcji języka C# 7,1, takich jak domyślne wyrażenia, wywnioskowane nazwy krotek i dopasowanie do wzorca przy użyciu typów ogólnych. Aby użyć języka C# 7,1 w projekcie, Dodaj następującą właściwość w pliku projektu, a następnie załaduj ponownie rozwiązanie:

```xml
<LangVersion>latest</LangVersion>
```

Aby uzyskać informacje o stanie funkcji języka C# 7,1, zobacz [repozytorium GitHub Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).

## <a name="other-documentation-updates-for-20"></a>Inne aktualizacje dokumentacji dotyczące 2,0

* [Profile publikowania programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles)
* [Zarządzanie kluczami](xref:security/data-protection/implementation/key-management)
* [Konfigurowanie uwierzytelniania w usłudze Facebook](xref:security/authentication/facebook-logins)
* [Konfigurowanie uwierzytelniania w usłudze Twitter](xref:security/authentication/twitter-logins)
* [Skonfiguruj uwierzytelnianie Google](xref:security/authentication/google-logins)
* [Konfigurowanie uwierzytelniania konta Microsoft](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a>Wskazówki dotyczące migracji

Aby uzyskać wskazówki dotyczące sposobu migracji aplikacji ASP.NET Core 1. x do ASP.NET Core 2,0, zobacz następujące zasoby:

* [Migrowanie z ASP.NET Core 1. x do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)
* [Migrowanie uwierzytelniania Identity i do ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji ASP.NET Core 2,0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).

Aby nawiązać połączenie z postępem i planami zespołu deweloperów ASP.NET Core, Dostosuj do [ASP.NET Community standup](https://live.asp.net/).
