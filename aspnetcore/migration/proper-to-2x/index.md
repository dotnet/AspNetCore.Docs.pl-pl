---
title: Migrowanie z ASP.NET do ASP.NET Core
author: isaac2004
description: Otrzymuj wskazówki dotyczące migrowania istniejących aplikacji ASP.NET MVC lub Web API do ASP.NET Core. Web
ms.author: scaddie
ms.date: 10/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/index
ms.openlocfilehash: 1530a5e6cc4b241b916ed7c64587b515186cc6ca
ms.sourcegitcommit: d306407dc5bfe6fdfbac482214b3f59371b582bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2020
ms.locfileid: "85951934"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrowanie z ASP.NET do ASP.NET Core

Autor [Tomasz Levin](https://isaaclevin.com)

Ten artykuł służy jako Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET do ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

[Zestaw .NET Core SDK 2,2 lub nowszy](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Platformy docelowe

Projekty ASP.NET Core oferują deweloperom elastyczność określania platformy .NET Core, .NET Framework lub obu. Dowiedz się, [jak wybrać platformę .NET Core i .NET Framework dla aplikacji serwerowych](/dotnet/standard/choosing-core-framework-server) , aby określić, która platforma docelowa jest najbardziej odpowiednia.

W przypadku .NET Framework określania wartości docelowej projekty muszą odwoływać się do poszczególnych pakietów NuGet.

Kierowanie programu .NET Core umożliwia eliminację wielu jawnych odwołań do pakietów, dzięki [czemu ASP.NET Core.](xref:fundamentals/metapackage-app) Zainstaluj `Microsoft.AspNetCore.App` pakiet w projekcie:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

W przypadku użycia pakietu z aplikacją nie są wdrażane żadne pakiety, do których odwołuje się pakiet. Magazyn środowiska uruchomieniowego .NET Core zawiera te zasoby i są wstępnie skompilowane w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [Microsoft. AspNetCore. App Package for ASP.NET Core](xref:fundamentals/metapackage-app) .

## <a name="project-structure-differences"></a>Różnice struktury projektu

Format pliku *. csproj* został uproszczony w ASP.NET Core. Niektóre istotne zmiany obejmują:

- Jawne dołączenie plików nie jest konieczne, aby były uważane za część projektu. Zmniejsza to ryzyko konfliktów scalania XML podczas pracy nad dużymi zespołami.
- Nie ma żadnych odwołań opartych na identyfikatorach GUID do innych projektów, co zwiększa czytelność plików.
- Plik można edytować bez zwalniania go w programie Visual Studio:

    ![Edytuj opcję menu kontekstowego CSPROJ w programie Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Zastąpienie pliku Global. asax

ASP.NET Core wprowadzono nowy mechanizm uruchamiania aplikacji. Punkt wejścia dla aplikacji ASP.NET to plik *Global. asax* . Zadania, takie jak konfiguracja tras oraz filtrowanie i rejestrowanie obszaru, są obsługiwane w pliku *Global. asax* .

[!code-csharp[](samples/globalasax-sample.cs)]

Takie podejście Couples aplikację i serwer, na który jest wdrażana w sposób, który zakłóca implementację. W celu oddzielenia [Owin](https://owin.org/) został wprowadzony w celu zapewnienia bardziej przejrzystego sposobu używania wielu struktur. OWIN zapewnia potok do dodawania tylko wymaganych modułów. Środowisko hostingu wykonuje funkcję [uruchamiania](xref:fundamentals/startup) , aby skonfigurować usługi i potok żądania aplikacji. `Startup`rejestruje zestaw programów pośredniczących w aplikacji. Dla każdego żądania aplikacja wywołuje każdy składnik pośredniczący ze wskaźnikiem głównym połączonej listy z istniejącym zestawem programów obsługi. Każdy składnik pośredniczący może dodać jeden lub więcej programów obsługi do potoku obsługi żądania. Jest to realizowane przez zwrócenie odwołania do programu obsługi, który jest nowym szefem listy. Każdy program obsługi jest odpowiedzialny za zapamiętywanie i wywoływanie kolejnej procedury obsługi na liście. W przypadku ASP.NET Core punkt wejścia do aplikacji jest `Startup` i nie ma już zależności od elementu *Global. asax*. W przypadku korzystania z programu OWIN z .NET Framework należy użyć podobnej do poniższej postaci potoku:

[!code-csharp[](samples/webapi-owin.cs)]

Powoduje to skonfigurowanie tras domyślnych i wartości domyślnych XmlSerialization w formacie JSON. W razie potrzeby Dodaj inne oprogramowanie pośredniczące (ładowanie usług, ustawień konfiguracji, plików statycznych itp.).

ASP.NET Core używa podobnego podejścia, ale nie polega na OWIN do obsługi wpisu. Zamiast tego robi to za pomocą metody *program.cs* `Main` (podobnie jak aplikacje konsolowe) i `Startup` jest ładowany w tym miejscu.

[!code-csharp[](samples/program.cs)]

`Startup`musi zawierać `Configure` metodę. W programie `Configure` Dodaj wymagane oprogramowanie pośredniczące do potoku. W poniższym przykładzie (z domyślnego szablonu witryny sieci Web) metody rozszerzenia konfigurują potok z obsługą:

- Strony błędów
- Zabezpieczenia protokołu HTTP Strict Transport
- Przekierowywanie HTTP do protokołu HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Host i aplikacja zostały odłączone, co zapewnia elastyczność przejścia do innej platformy w przyszłości.

> [!NOTE]
> Aby uzyskać bardziej szczegółowe informacje dotyczące ASP.NET Core uruchamiania i oprogramowania pośredniczącego, zobacz [Uruchamianie w ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Konfiguracje magazynu

ASP.NET obsługuje przechowywanie ustawień. Te ustawienia są używane na przykład w celu obsługi środowiska, w którym aplikacje zostały wdrożone. Typowym celem jest przechowywanie wszystkich niestandardowych par klucz-wartość w `<appSettings>` sekcji pliku *Web.config* :

[!code-xml[](samples/webconfig-sample.xml)]

Aplikacje odczytują te ustawienia przy użyciu `ConfigurationManager.AppSettings` kolekcji w `System.Configuration` przestrzeni nazw:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core może przechowywać dane konfiguracyjne dla aplikacji w dowolnym pliku i ładować je w ramach uruchamiania oprogramowania pośredniczącego. Domyślny plik używany w szablonach projektu jest *appsettings.jsw*:

[!code-json[](samples/appsettings-sample.json)]

Załadowanie tego pliku do wystąpienia `IConfiguration` wewnątrz aplikacji odbywa się w *Startup.cs*:

[!code-csharp[](samples/startup-builder.cs)]

Aplikacja odczytuje z `Configuration` programu, aby pobrać ustawienia:

[!code-csharp[](samples/read-appsettings.cs)]

Istnieją rozszerzenia tego podejścia, aby proces był bardziej niezawodny, na przykład przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection) (di) do załadowania usługi z tymi wartościami. Podejście DI zapewnia zestaw obiektów konfiguracji o jednoznacznie określonym typie.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Aby uzyskać bardziej szczegółowe informacje na temat konfiguracji ASP.NET Core, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Natywny wtrysk zależności

Ważnym celem tworzenia dużych, skalowalnych aplikacji jest swobodne sprzęganie składników i usług. [Iniekcja zależności](xref:fundamentals/dependency-injection) jest popularną techniką do osiągnięcia tego celu i jest natywnym składnikiem ASP.NET Core.

W aplikacjach ASP.NET deweloperzy korzystają z biblioteki innej firmy w celu zaimplementowania iniekcji zależności. Jedną z takich bibliotek jest platforma [Unity](https://github.com/unitycontainer/unity), świadczona przez wzorce firmy Microsoft & praktyk.

Przykładem konfiguracji iniekcji zależności przy użyciu aparatu Unity jest implementacja `IDependencyResolver` , która zawija `UnityContainer` :

[!code-csharp[](samples/sample8.cs)]

Utwórz wystąpienie obiektu `UnityContainer` , zarejestruj swoją usługę i Ustaw program rozpoznawania zależności na `HttpConfiguration` nowe wystąpienie `UnityResolver` dla kontenera:

[!code-csharp[](samples/sample9.cs)]

Wstrzyknięcie w `IProductRepository` razie konieczności:

[!code-csharp[](samples/sample5.cs)]

Ponieważ iniekcja zależności jest częścią ASP.NET Core, możesz dodać swoją usługę w `ConfigureServices` metodzie *Startup.cs*:

[!code-csharp[](samples/configure-services.cs)]

Repozytorium można wstrzyknąć w dowolnym miejscu, podobnie jak w przypadku aparatu Unity.

> [!NOTE]
> Aby uzyskać więcej informacji na temat iniekcji zależności, zobacz [iniekcja zależności](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Obsługuj pliki statyczne

Ważną częścią programowania w sieci Web jest możliwość obsłużynia statycznych zasobów po stronie klienta. Najczęstszymi przykładami plików statycznych są HTML, CSS, JavaScript i obrazy. Te pliki muszą być zapisane w opublikowanej lokalizacji aplikacji (lub sieci CDN) i przywoływane, tak aby mogły zostać załadowane przez żądanie. Ten proces został zmieniony w ASP.NET Core.

W ASP.NET pliki statyczne są przechowywane w różnych katalogach i przywoływane w widokach.

W ASP.NET Core pliki statyczne są przechowywane w "katalogu głównym sieci Web" (* &lt; &gt; /wwwroot zawartości*), chyba że zostały skonfigurowane inaczej. Pliki są ładowane do potoku żądania przez wywołanie `UseStaticFiles` metody rozszerzenia z `Startup.Configure` :

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Jeśli .NET Framework określania wartości docelowej, zainstaluj pakiet NuGet `Microsoft.AspNetCore.StaticFiles` .

Na przykład zasób obrazu w folderze *wwwroot/images* jest dostępny dla przeglądarki w lokalizacji takiej jak `http://<app>/images/<imageFileName>` .

> [!NOTE]
> Aby uzyskać bardziej szczegółowe informacje na temat obsługi plików statycznych w ASP.NET Core, zobacz [pliki statyczne](xref:fundamentals/static-files).

## <a name="multi-value-cookies"></a>Wiele wartości plików cookie

[Wielowartościowe pliki cookie](xref:System.Web.HttpCookie.Values) nie są obsługiwane w ASP.NET Core. Utwórz jeden plik cookie na wartość.

## <a name="partial-app-migration"></a>Migracja częściowej aplikacji

Jednym z metod migracji części aplikacji jest utworzenie aplikacji podrzędnej IIS i przeniesienie niektórych tras z ASP.NET 4. x do ASP.NET Core przy zachowaniu struktury adresu URL aplikacji. Rozważmy na przykład strukturę adresu URL aplikacji z pliku *applicationHost.config* :

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Struktura katalogów:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Przenoszenie bibliotek do programu .NET Core](/dotnet/core/porting/libraries)
