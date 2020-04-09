---
title: Migracja z ASP.NET do ASP.NET Core
author: isaac2004
description: Uzyskaj wskazówki dotyczące migracji istniejących aplikacji ASP.NET MVC lub Web API do ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511095"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migracja z ASP.NET do ASP.NET Core

Przez [Isaac Levin](https://isaaclevin.com)

Ten artykuł służy jako przewodnik do migracji ASP.NET aplikacji do ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

[.NET Core SDK 2.2 lub nowsza](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Platformy docelowe

ASP.NET Core oferują deweloperom elastyczność kierowania .NET Core, .NET Framework lub obu. Zobacz [Wybieranie między .NET Core i .NET Framework dla aplikacji serwera,](/dotnet/standard/choosing-core-framework-server) aby określić, która struktura docelowa jest najbardziej odpowiednia.

Podczas kierowania .NET Framework, projekty muszą odwoływać się do poszczególnych pakietów NuGet.

Targetowanie .NET Core pozwala wyeliminować wiele wyraźnych odniesień do pakietów, dzięki [ASP.NET Core metapackage](xref:fundamentals/metapackage-app). Zainstaluj `Microsoft.AspNetCore.App` metapakiet w swoim projekcie:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Gdy metapakiet jest używany, żadne pakiety, do których odwołuje się w metapakiet są wdrażane z aplikacją. Magazyn środowiska uruchomieniowego .NET Core zawiera te zasoby i są one wstępnie kompilowane w celu zwiększenia wydajności. Aby uzyskać więcej informacji, zobacz [metapakiet Microsoft.AspNetCore.App dla ASP.NET Core.](xref:fundamentals/metapackage-app)

## <a name="project-structure-differences"></a>Różnice w strukturze projektu

Format pliku *csproj* został uproszczony w ASP.NET Core. Oto kilka znaczących zmian:

- Jawne włączenie plików nie jest konieczne, aby były one uważane za część projektu. Zmniejsza to ryzyko konfliktów scalania XML podczas pracy z dużymi zespołami.
- Nie ma żadnych odwołań opartych na identyfikatorze GUID do innych projektów, co zwiększa czytelność plików.
- Plik można edytować bez zwalniania go w programie Visual Studio:

    ![Opcja menu kontekstowego programu CSPROJ w programie Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Wymiana pliku Global.asax

ASP.NET Core wprowadziła nowy mechanizm uruchamiania aplikacji. Punktem wejścia dla aplikacji ASP.NET jest plik *Global.asax.* Zadania, takie jak konfiguracja trasy i filtrowanie i rejestracje obszarów, są obsługiwane w pliku *Global.asax.*

[!code-csharp[](samples/globalasax-sample.cs)]

Takie podejście łączy aplikację i serwer, na którym jest wdrożony w sposób, który zakłóca implementację. W celu oddzielenia, [OWIN](https://owin.org/) został wprowadzony w celu zapewnienia czystszego sposobu korzystania z wielu struktur razem. OWIN udostępnia potok, aby dodać tylko moduły potrzebne. Środowisko hostingu przyjmuje funkcję [Uruchamianie,](xref:fundamentals/startup) aby skonfigurować usługi i potok żądań aplikacji. `Startup`rejestruje zestaw oprogramowania pośredniczącego z aplikacją. Dla każdego żądania aplikacja wywołuje każdy ze składników oprogramowania pośredniczącego ze wskaźnikiem head połączonej listy do istniejącego zestawu programów obsługi. Każdy składnik oprogramowania pośredniczącego można dodać jeden lub więcej programów obsługi do potoku obsługi żądań. Jest to realizowane przez zwrócenie odwołania do programu obsługi, który jest nowy szef listy. Każdy program obsługi jest odpowiedzialny za zapamiętywanie i wywoływanie następnego programu obsługi na liście. W ASP.NET Core punktem wejścia do aplikacji `Startup`jest i nie masz już zależności od *global.asax*. Korzystając z funkcji OWIN z programem .NET Framework, użyj następującej opcji jako potoku:

[!code-csharp[](samples/webapi-owin.cs)]

Spowoduje to skonfigurowanie tras domyślnych i domyślnie XmlSerialization za pomocą Json. W razie potrzeby dodaj inne oprogramowanie pośredniczące do tego potoku (usługi ładowania, ustawienia konfiguracji, pliki statyczne itp.).

ASP.NET Core używa podobnego podejścia, ale nie polega na OWIN do obsługi wpisu. Zamiast tego odbywa się to *Program.cs* `Main` za pomocą metody Program.cs (podobnej do aplikacji konsoli) i `Startup` jest tam ładowana.

[!code-csharp[](samples/program.cs)]

`Startup`musi zawierać `Configure` metodę. W `Configure`, dodaj niezbędne oprogramowanie pośredniczące do potoku. W poniższym przykładzie (z domyślnego szablonu witryny sieci Web) metody rozszerzenia konfigurują potok z obsługą:

- Strony błędów
- Ścisłe bezpieczeństwo transportu HTTP
- Przekierowanie HTTP do protokołu HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Host i aplikacja zostały oddzielone, co zapewnia elastyczność przenoszenia do innej platformy w przyszłości.

> [!NOTE]
> Aby uzyskać bardziej szczegółowe odniesienie do ASP.NET core startup i middleware, zobacz [Uruchamianie w ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Konfiguracje sklepu

ASP.NET obsługuje przechowywanie ustawień. Te ustawienie jest używane, na przykład do obsługi środowiska, w którym aplikacje zostały wdrożone. Powszechną praktyką było przechowywanie wszystkich niestandardowych `<appSettings>` par klucz-wartość w sekcji pliku *Web.config:*

[!code-xml[](samples/webconfig-sample.xml)]

Aplikacje odczytywać `ConfigurationManager.AppSettings` te `System.Configuration` ustawienia przy użyciu kolekcji w obszarze nazw:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core może przechowywać dane konfiguracyjne dla aplikacji w dowolnym pliku i ładować je jako część uruchamiania oprogramowania pośredniczącego. Domyślnym plikiem używanym w szablonach projektu jest *appsettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Ładowanie tego pliku do `IConfiguration` wystąpienia wewnątrz aplikacji odbywa się w *Startup.cs:*

[!code-csharp[](samples/startup-builder.cs)]

Aplikacja odczytuje `Configuration` z aby uzyskać ustawienia:

[!code-csharp[](samples/read-appsettings.cs)]

Istnieją rozszerzenia tego podejścia, aby proces bardziej niezawodne, takie jak przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection) (DI) do ładowania usługi z tymi wartościami. Podejście DI zapewnia silnie typiwany zestaw obiektów konfiguracji.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Aby uzyskać bardziej szczegółowe odniesienie do ASP.NET konfiguracji rdzenia, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Wstrzyknięcie zależności natywnej

Ważnym celem podczas tworzenia dużych, skalowalnych aplikacji jest luźne sprzężenie komponentów i usług. [Wtrysk zależności](xref:fundamentals/dependency-injection) jest popularną techniką osiągnięcia tego celu i jest natywnym składnikiem ASP.NET Core.

W ASP.NET aplikacji deweloperzy polegają na bibliotece innej firmy w celu zaimplementowania iniekcji zależności. Jedną z takich bibliotek jest [Unity](https://github.com/unitycontainer/unity), dostarczone przez Microsoft Patterns & Praktyki.

Przykład konfigurowania iniekcji zależności z `IDependencyResolver` Unity implementuje, że zawija: `UnityContainer`

[!code-csharp[](samples/sample8.cs)]

Utwórz wystąpienie `UnityContainer`usługi , zarejestruj usługę i ustaw `HttpConfiguration` program rozpoznawania zależności `UnityResolver` na nowe wystąpienie kontenera:

[!code-csharp[](samples/sample9.cs)]

Wstrzyknąć `IProductRepository` w razie potrzeby:

[!code-csharp[](samples/sample5.cs)]

Ponieważ iniekcja zależności jest częścią ASP.NET Core, można `ConfigureServices` dodać usługę w metodzie *Startup.cs:*

[!code-csharp[](samples/configure-services.cs)]

Repozytorium można wstrzyknąć w dowolnym miejscu, tak jak miało to miejsce w przypadku Unity.

> [!NOTE]
> Aby uzyskać więcej informacji na temat iniekcji zależności, zobacz [Iniekcja zależności.](xref:fundamentals/dependency-injection)

## <a name="serve-static-files"></a>Obsługa plików statycznych

Ważną częścią tworzenia stron internetowych jest możliwość obsługi statycznych zasobów po stronie klienta. Najczęstszymi przykładami plików statycznych są HTML, CSS, Javascript i obrazy. Pliki te muszą być zapisane w opublikowanej lokalizacji aplikacji (lub CDN) i odwołuje się, aby można je było załadować przez żądanie. Ten proces zmienił się w ASP.NET Core.

W ASP.NET pliki statyczne są przechowywane w różnych katalogach i odwołuje się w widokach.

W ASP.NET Core pliki statyczne są przechowywane w "katalogu głównym sieci Web"*&lt;(root&gt;treści /wwwroot),* chyba że skonfigurowano inaczej. Pliki są ładowane do potoku `UseStaticFiles` żądań, wywołując metodę rozszerzenia z: `Startup.Configure`

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Jeśli jest kierowana na platformę `Microsoft.AspNetCore.StaticFiles`.NET Framework, zainstaluj pakiet NuGet .

Na przykład zasób obrazu w folderze *wwwroot/images* jest dostępny `http://<app>/images/<imageFileName>`dla przeglądarki w lokalizacji, takiej jak .

> [!NOTE]
> Aby uzyskać bardziej szczegółowe odniesienie do wyświetlania plików statycznych w ASP.NET Core, zobacz [Pliki statyczne](xref:fundamentals/static-files).

## <a name="multi-value-cookies"></a>Wielowartowiowe pliki cookie

[Pliki cookie o wielu wartościach](xref:System.Web.HttpCookie.Values) nie są obsługiwane w ASP.NET Core. Utwórz jeden plik cookie na wartość.

## <a name="partial-app-migration"></a>Częściowa migracja aplikacji

Jednym z podejść do częściowej migracji aplikacji jest utworzenie poddołty iIS i tylko przenieść niektóre trasy z ASP.NET 4.x do ASP.NET Core przy jednoczesnym zachowaniu struktury adresu URL aplikacji. Rozważmy na przykład strukturę adresu URL aplikacji z pliku *applicationHost.config:*

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

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przenoszenie bibliotek do rdzenia NET](/dotnet/core/porting/libraries)
