---
title: Używanie zestawów uruchamiania hostingu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ulepszyć aplikację core ASP.NET z zestawu zewnętrznego przy użyciu implementacji IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417623"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Używanie zestawów uruchamiania hostingu w ASP.NET Core

Przez [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) dodaje ulepszenia do aplikacji podczas uruchamiania z zestawu zewnętrznego. Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowe dostawcy konfiguracji lub usługi do aplikacji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atrybut HostingStartup

A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut wskazuje obecność zestawu uruchamiania hostingu, aby aktywować w czasie wykonywania.

Zestaw wejścia lub zestaw zawierający `Startup` klasę jest automatycznie `HostingStartup` skanowany w poszukiwaniu atrybutu. Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Lista zestawów do wykluczenia z odnajdywania jest ładowana z [webhostdefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

W poniższym przykładzie obszar nazw zestawu `StartupEnhancement`uruchamiania hostingu jest . Klasa zawierająca kod startowy `StartupEnhancementHostingStartup`hostingu to:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy `IHostingStartup` implementacji zestawu uruchamiania hostingu.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Odkryj załadowane zestawy startowe hostingu

Aby odkryć załadowane zestawy uruchamiania hostingu, włącz rejestrowanie i sprawdź dzienniki aplikacji. Błędy, które występują podczas ładowania zestawów są rejestrowane. Załadowane zestawy uruchamiania hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Wyłącz automatyczne ładowanie zestawów uruchamiania hostingu

Aby wyłączyć automatyczne ładowanie zestawów uruchamiania hostingu, należy użyć jednego z następujących metod:

* Aby zapobiec załadunku wszystkich zestawów uruchamiania `1`hostingu, ustaw jeden z następujących opcji na: `true`

  * Zapobiegaj ustawieniu konfiguracji hosta hosta uruchamiania hostingu:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`zmienną środowiskową.

* Aby zapobiec załadunku określonych zestawów uruchamiania hostingu, ustaw jeden z następujących ciągów rozdzielanych średnikami zestawów uruchamiania hostingu, aby wykluczyć podczas uruchamiania:

  * Ustawienie konfiguracji hosta hosta zestawów wykluczeń uruchamiania hostingu:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`zmienną środowiskową.

Jeśli ustawiono zarówno ustawienie konfiguracji hosta, jak i zmienną środowiskową, ustawienie hosta steruje zachowaniem.

Wyłączenie zestawów uruchamiania hostingu przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.

## <a name="project"></a>Projekt

Utwórz uruchamianie hostingu z jednym z następujących typów projektów:

* [Biblioteka klas](#class-library)
* [Aplikacja konsoli bez punktu wejścia](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteka klas

Rozszerzenie uruchamiania hostingu można zapewnić w bibliotece klas. Biblioteka zawiera `HostingStartup` atrybut.

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera aplikację Razor Pages, *HostingStartupApp*i bibliotekę *klas, HostingStartupLibrary*. Biblioteka klas:

* Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje . `ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji przy użyciu dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zawiera `HostingStartup` atrybut identyfikujący obszar nazw i klasę uruchamiania hostingu.

Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania biblioteki klas hostingu:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*. Pakiet ma te same cechy biblioteki klas opisane wcześniej. Pakiet:

* Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje . `ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji.
* Zawiera `HostingStartup` atrybut.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania hostowania pakietu:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplikacja konsoli bez punktu wejścia

*Takie podejście jest dostępne tylko dla aplikacji .NET Core, a nie .NET Framework.*

Dynamiczne wzmocnienie uruchamiania hostingu, które nie wymaga odwołania do czasu kompilacji dla aktywacji, `HostingStartup` może być podane w aplikacji konsoli bez punktu wejścia, który zawiera atrybut. Publikowanie aplikacji konsoli tworzy zestaw uruchamiania hostingu, który może być zużywany z magazynu środowiska wykonawczego.

W tym procesie jest używana aplikacja konsoli bez punktu wejścia, ponieważ:

* Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu. Plik zależności jest uruchamianym zasobem aplikacji, który jest produkowany przez publikowanie aplikacji, a nie biblioteki.
* Nie można dodać biblioteki bezpośrednio do [magazynu pakietów środowiska wykonawczego,](/dotnet/core/deploying/runtime-store)co wymaga projektu możliwego do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.

W tworzeniu dynamicznego uruchamiania hostingu:

* Zestaw uruchamiania hostingu jest tworzony z aplikacji konsoli bez punktu wejścia, który:
  * Zawiera klasę, która `IHostingStartup` zawiera implementację.
  * Zawiera [atrybut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do `IHostingStartup` identyfikowania klasy implementacji.
* Aplikacja konsoli jest publikowana w celu uzyskania zależności uruchamiania hostingu. Konsekwencją publikowania aplikacji konsoli jest, że nieużywane zależności są przycinane z pliku zależności.
* Plik zależności jest modyfikowany w celu ustawienia lokalizacji środowiska wykonawczego zestawu uruchamiania hostingu.
* Zestaw uruchamiania hostingu i jego plik zależności jest umieszczany w magazynie pakietów środowiska wykonawczego. Aby odkryć zestaw uruchamiania hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.

Aplikacja konsoli odwołuje się do pakietu [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut identyfikuje klasę jako `IHostingStartup` implementację do ładowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>i wykonywania podczas tworzenia . W poniższym przykładzie obszar `StartupEnhancement`nazw jest `StartupEnhancementHostingStartup`, a klasa jest:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Klasa implementuje `IHostingStartup`. <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `IHostingStartup.Configure`w zestawie uruchamiania hostingu jest `Startup.Configure` wywoływana przez środowisko wykonawcze przed w kodzie użytkownika, co pozwala kod użytkownika zastąpić dowolną konfigurację zapewnianą przez zestaw uruchamiania hostingu.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Podczas tworzenia `IHostingStartup` projektu plik zależności (*.deps.json* `runtime` ) ustawia lokalizację złożenia w folderze *bin:*

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Wyświetlana jest tylko część pliku. Nazwa zestawu w przykładzie jest `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfiguracja zapewniana przez startup hostingu

Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma pierwszeństwo:

1. Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji za pomocą, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację po wykonaniu delegatów aplikacji. Konfiguracja uruchamiania hostingu ma pierwszeństwo przed konfiguracją aplikacji przy użyciu tej metody.
1. Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> aplikacji przy użyciu, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację przed wykonaniem delegatów aplikacji. Wartości konfiguracji aplikacji mają pierwszeństwo przed wartościami dostarczonymi przez startup hostingu przy użyciu tej metody.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Określanie zestawu uruchamiania hostingu

W przypadku uruchamiania hostingu dostarczanego przez bibliotekę klas lub konsolę `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` należy określić nazwę zestawu uruchamiania hostingu w zmiennej środowiskowej. Zmienna środowiskowa jest listą zestawów rozdzielanych średnikami.

Tylko zestawy uruchamiania hostingu `HostingStartup` są skanowane w celu uzyskania atrybutu. Dla przykładowej aplikacji *HostingStartupApp*, aby odkryć startupy hostingu opisane wcześniej, zmienna środowiskowa jest ustawiona na następującą wartość:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Zestaw uruchamiania hostingu można również ustawić przy użyciu ustawienia konfiguracji hosta zestawy uruchamiania hostingu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Gdy istnieje wiele zestawów uruchamiania hostingu, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w którym są wymienione zestawy.

## <a name="activation"></a>Aktywacja

Opcje aktywacji uruchamiania hostingu to:

* [Aktywacja magazynu](#runtime-store) &ndash; środowiska uruchomieniowego nie wymaga odwołania do czasu kompilacji do aktywacji. Przykładowa aplikacja umieszcza zestaw uruchamiania hostingu i zależności plików do folderu, *wdrożenia*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku multimachine. Folder *wdrażania* zawiera również skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby włączyć uruchamianie hostingu.
* Odwołanie do czasu kompilacji wymagane do aktywacji
  * [Pakiet NuGet](#nuget-package)
  * [Folder pojemnika projektu](#project-bin-folder)

### <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Implementacja uruchamiania hostingu jest umieszczana w [magazynie środowiska wykonawczego](/dotnet/core/deploying/runtime-store). Odwołanie w czasie kompilacji do zestawu nie jest wymagane przez aplikację rozszerzone.

Po zbudowaniu uruchamiania hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet.](/dotnet/core/tools/dotnet-store)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

W przykładowej aplikacji (projekt*RuntimeStore)* używane jest następujące polecenie:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

W środowisku uruchomieniowym, aby odnajdywać magazyn środowiska `DOTNET_SHARED_STORE` wykonawczego, lokalizacja magazynu środowiska wykonawczego jest dodawany do zmiennej środowiskowej.

**Modyfikowanie i umieszczanie pliku zależności uruchamiania hostingu**

Aby uaktywnić ulepszenie bez odwołania do pakietu do `additionalDeps`ulepszenia, należy określić dodatkowe zależności do środowiska wykonawczego za pomocą programu . `additionalDeps`pozwala na:

* Rozszerz wykres biblioteki aplikacji, udostępniając zestaw dodatkowych plików *.deps.json* do scalenia z własnym plikiem *deps.json* aplikacji podczas uruchamiania.
* Spraw, aby zestaw uruchamiania hostingu był wykrywalny i ładowalny.

Zalecane podejście do generowania pliku dodatkowych zależności jest:

 1. Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska wykonawczego, do którego odwołuje się poprzednia sekcja.
 1. Usuń odwołanie do manifestu `runtime` z bibliotek i sekcji wynikowego pliku *.deps.json.*

W przykładowym projekcie `store.manifest/1.0.0` właściwość jest `targets` `libraries` usuwana z sekcji i:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umieść plik *deps.json* w następującej lokalizacji:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Lokalizacja dodana `DOTNET_ADDITIONAL_DEPS` do zmiennej środowiskowej.
* `{SHARED FRAMEWORK NAME}`&ndash; Udostępnione framework wymagane dla tego pliku dodatkowych zależności.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimalna wersja współdzielonej struktury.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Nazwa zestawu ulepszenia.

W przykładowej aplikacji (projekt*RuntimeStore)* plik dodatkowych zależności jest umieszczany w następującej lokalizacji:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

W środowisku uruchomieniowym, aby odnajdywać lokalizację `DOTNET_ADDITIONAL_DEPS` magazynu środowiska wykonawczego, dodatkowa lokalizacja pliku zależności jest dodawana do zmiennej środowiskowej.

W przykładowej aplikacji (Projekt*RuntimeStore)* tworzenie magazynu środowiska wykonawczego i generowanie pliku dodatkowych zależności odbywa się za pomocą skryptu [programu PowerShell.](/powershell/scripting/powershell-scripting)

Aby zapoznać się z przykładami ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych, zobacz [Używanie wielu środowisk](xref:fundamentals/environments).

**Wdrożenie**

Aby ułatwić wdrażanie uruchamiania hostingu w środowisku wielomatynowym, przykładowa aplikacja tworzy folder *wdrażania* w opublikowanych danych wyjściowych, który zawiera:

* Magazyn środowiska uruchomieniowego uruchamiania hostingu.
* Plik zależności uruchamiania hostingu.
* Skrypt programu PowerShell, który `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`tworzy `DOTNET_SHARED_STORE`lub `DOTNET_ADDITIONAL_DEPS` modyfikuje program , i obsługuje aktywację uruchamiania hostingu. Uruchom skrypt z administracyjnego wiersza polecenia programu PowerShell w systemie wdrażania.

### <a name="nuget-package"></a>Pakiet NuGet

Wzmocnienie uruchamiania hostingu mogą być dostarczane w pakiecie NuGet. Pakiet ma `HostingStartup` atrybut. Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Rozszerzony plik projektu aplikacji sprawia, że odwołanie do pakietu dla uruchamiania hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji). Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*). Takie podejście ma zastosowanie do pakietu zestawu uruchamiania hostingu opublikowanego [w nuget.org](https://www.nuget.org/).
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).

Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska wykonawczego, zobacz następujące tematy:

* [Jak utworzyć pakiet NuGet za pomocą narzędzi wieloplatformowych](/dotnet/core/deploying/creating-nuget-packages)
* [Publikowanie pakietów](/nuget/create-packages/publish-a-package)
* [Magazyn pakietu środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Folder pojemnika projektu

Ulepszenie uruchamiania hostingu może być dostarczone przez zestaw wdrożony w *pojemniku*w ulepszonej aplikacji. Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednego z następujących metod:

* Rozszerzony plik projektu aplikacji sprawia, że odwołanie do zestawu do uruchamiania hostingu (odwołanie w czasie kompilacji). Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*). Takie podejście ma zastosowanie, gdy scenariusz wdrażania wymaga dokonania odwołania w czasie kompilacji do zestawu uruchamiania hostingu (plik*dll)* i przenoszenia zestawu do:
  * Projekt zużywający.
  * Lokalizacja dostępna dla projektu zużywającego.
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).
* Podczas kierowania na .NET Framework, zestaw jest wczytywany w kontekście obciążenia domyślnego, co w programie .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:
  * Ścieżka &ndash; podstawowa aplikacji Folder *bin,* w którym znajduje się plik wykonywalny aplikacji (*.exe).*
  * Globalna pamięć podręczna &ndash; zestawów (GAC) GaC przechowuje zestawy współużytkowane przez kilka aplikacji platformy .NET Framework. Aby uzyskać więcej informacji, zobacz [Jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji programu .NET Framework.

## <a name="sample-code"></a>Przykładowy kod

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)demonstruje scenariusze implementacji uruchamiania hostingu:

* Dwa zestawy uruchamiania hostingu (biblioteki klas) ustawiają parę par klucza-wartości konfiguracji w pamięci:
  * Pakiet NuGet (*HostingStartupPackage*)
  * Biblioteka klas (*HostingStartupLibrary*)
* Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w magazynie uruchomieniowym *(StartupDiagnostics*). Zestaw dodaje dwa oprogramowanie pośredniczące do aplikacji podczas uruchamiania, które zawierają informacje diagnostyczne na:
  * Zarejestrowane usługi
  * Adres (schemat, host, podstawa ścieżki, ścieżka, ciąg zapytania)
  * Połączenie (zdalny adres IP, port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)
  * Nagłówki żądań
  * Zmienne środowiskowe

Aby uruchomić próbkę:

**Aktywacja z pakietu NuGet**

1. Skompiluj pakiet *HostingStartupPackage* za pomocą polecenia [dotnet pack.](/dotnet/core/tools/dotnet-pack)
1. Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.
1. Skompiluj i uruchom aplikację. Odwołanie do pakietu jest obecny w aplikacji rozszerzonej (odwołanie w czasie kompilacji). A `<PropertyGroup>` w pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/debug)* jako źródło pakietu. Dzięki temu aplikacja może korzystać z pakietu bez przesyłania pakietu do [nuget.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę pakietu.

Jeśli wniesiesz zmiany do projektu *HostingStartupPackage* i ponownie skompilować go, wyczyść lokalne pamięci podręczne pakietu NuGet, aby upewnić się, że *HostingStartupApp* odbiera zaktualizowany pakiet, a nie nieaktualny pakiet z lokalnej pamięci podręcznej. Aby wyczyścić lokalne bufory NuGet, wykonaj następujące polecenie [dotnet nuget locals:](/dotnet/core/tools/dotnet-nuget-locals)

```dotnetcli
dotnet nuget locals all --clear
```

**Aktywacja z biblioteki klas**

1. Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [dotnet build.](/dotnet/core/tools/dotnet-build)
1. Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`
1. *bin*-deploy zestawu biblioteki klas do aplikacji przez skopiowanie *pliku HostingStartupLibrary.dll* z biblioteki klas skompilowanych danych wyjściowych do folderu *bin/debug* aplikacji.
1. Skompiluj i uruchom aplikację. Plik `<ItemGroup>` projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odwołanie do czasu kompilacji). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę biblioteki klas.

**Aktywacja z zestawu wdrożonego w magazynie uruchomieniowym**

1. Projekt *StartupDiagnostics* używa programu [PowerShell](/powershell/scripting/powershell-scripting) do modyfikowania pliku *StartupDiagnostics.deps.json.* Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatku SP1 i dodatku SP1 dla systemu Windows Server 2008 R2. Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).
1. Wykonaj skrypt *build.ps1* w folderze *RuntimeStore.* Skrypt:
   * Generuje `StartupDiagnostics` pakiet w folderze *obj\packages.*
   * Generuje magazyn środowiska wykonawczego `StartupDiagnostics` w folderze *magazynu.* Polecenie `dotnet store` w skrypcie `win7-x64` używa [identyfikatora środowiska wykonawczego (RID)](/dotnet/core/rid-catalog) dla uruchamiania hostingu wdrożonego w systemie Windows. Podczas dostarczania uruchamiania hostingu dla innego środowiska uruchomieniowego, zastąp poprawny rid w wierszu 37 skryptu. Magazyn środowiska wykonawczego `StartupDiagnostics` zostanie później przeniesiony do magazynu środowiska wykonawczego użytkownika lub systemu na komputerze, na którym zostanie wykorzystany zestaw. Lokalizacja instalacji magazynu uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.
   * Generuje for `additionalDeps` `StartupDiagnostics` w *folderze additionalDeps.* Dodatkowe zależności zostaną później przeniesione do dodatkowych zależności użytkownika lub systemu. Lokalizacją `StartupDiagnostics` instalacji dodatkowych zależności użytkownika jest *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.
   * Umieszcza plik *deploy.ps1* w folderze *wdrażania.*
1. Uruchom skrypt *deploy.ps1* w folderze *wdrażania.* Skrypt dołącza:
   * `StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.
   * Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu `DOTNET_ADDITIONAL_DEPS` RuntimeStore) do zmiennej środowiskowej.
   * Ścieżka magazynu środowiska wykonawczego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej. `DOTNET_SHARED_STORE`
1. Uruchom przykładową aplikację.
1. Poproś `/services` o punkt końcowy, aby wyświetlić zarejestrowane usługi aplikacji. Poproś `/diag` o punkt końcowy, aby wyświetlić informacje diagnostyczne.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) dodaje ulepszenia do aplikacji podczas uruchamiania z zestawu zewnętrznego. Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowe dostawcy konfiguracji lub usługi do aplikacji.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atrybut HostingStartup

A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut wskazuje obecność zestawu uruchamiania hostingu, aby aktywować w czasie wykonywania.

Zestaw wejścia lub zestaw zawierający `Startup` klasę jest automatycznie `HostingStartup` skanowany w poszukiwaniu atrybutu. Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). Lista zestawów do wykluczenia z odnajdywania jest ładowana z [webhostdefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Aby uzyskać więcej informacji, zobacz [Host sieci Web: Zestawy uruchamiania hostingu](xref:fundamentals/host/web-host#hosting-startup-assemblies) i [host sieci Web: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

W poniższym przykładzie obszar nazw zestawu `StartupEnhancement`uruchamiania hostingu jest . Klasa zawierająca kod startowy `StartupEnhancementHostingStartup`hostingu to:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy `IHostingStartup` implementacji zestawu uruchamiania hostingu.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Odkryj załadowane zestawy startowe hostingu

Aby odkryć załadowane zestawy uruchamiania hostingu, włącz rejestrowanie i sprawdź dzienniki aplikacji. Błędy, które występują podczas ładowania zestawów są rejestrowane. Załadowane zestawy uruchamiania hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Wyłącz automatyczne ładowanie zestawów uruchamiania hostingu

Aby wyłączyć automatyczne ładowanie zestawów uruchamiania hostingu, należy użyć jednego z następujących metod:

* Aby zapobiec załadunku wszystkich zestawów uruchamiania `1`hostingu, ustaw jeden z następujących opcji na: `true`
  * Zapobiegaj ustawieniu konfiguracji hosta [hosta uruchamiania hostingu.](xref:fundamentals/host/web-host#prevent-hosting-startup)
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP`zmienną środowiskową.
* Aby zapobiec załadunku określonych zestawów uruchamiania hostingu, ustaw jeden z następujących ciągów rozdzielanych średnikami zestawów uruchamiania hostingu, aby wykluczyć podczas uruchamiania:
  * Ustawienie konfiguracji hosta [hosta wykluczeń uruchamiania hostingu.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`zmienną środowiskową.

Jeśli ustawiono zarówno ustawienie konfiguracji hosta, jak i zmienną środowiskową, ustawienie hosta steruje zachowaniem.

Wyłączenie zestawów uruchamiania hostingu przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.

## <a name="project"></a>Projekt

Utwórz uruchamianie hostingu z jednym z następujących typów projektów:

* [Biblioteka klas](#class-library)
* [Aplikacja konsoli bez punktu wejścia](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteka klas

Rozszerzenie uruchamiania hostingu można zapewnić w bibliotece klas. Biblioteka zawiera `HostingStartup` atrybut.

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera aplikację Razor Pages, *HostingStartupApp*i bibliotekę *klas, HostingStartupLibrary*. Biblioteka klas:

* Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje . `ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji przy użyciu dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Zawiera `HostingStartup` atrybut identyfikujący obszar nazw i klasę uruchamiania hostingu.

Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania biblioteki klas hostingu:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*. Pakiet ma te same cechy biblioteki klas opisane wcześniej. Pakiet:

* Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje . `ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji.
* Zawiera `HostingStartup` atrybut.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania hostowania pakietu:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplikacja konsoli bez punktu wejścia

*Takie podejście jest dostępne tylko dla aplikacji .NET Core, a nie .NET Framework.*

Dynamiczne wzmocnienie uruchamiania hostingu, które nie wymaga odwołania do czasu kompilacji dla aktywacji, `HostingStartup` może być podane w aplikacji konsoli bez punktu wejścia, który zawiera atrybut. Publikowanie aplikacji konsoli tworzy zestaw uruchamiania hostingu, który może być zużywany z magazynu środowiska wykonawczego.

W tym procesie jest używana aplikacja konsoli bez punktu wejścia, ponieważ:

* Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu. Plik zależności jest uruchamianym zasobem aplikacji, który jest produkowany przez publikowanie aplikacji, a nie biblioteki.
* Nie można dodać biblioteki bezpośrednio do [magazynu pakietów środowiska wykonawczego,](/dotnet/core/deploying/runtime-store)co wymaga projektu możliwego do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.

W tworzeniu dynamicznego uruchamiania hostingu:

* Zestaw uruchamiania hostingu jest tworzony z aplikacji konsoli bez punktu wejścia, który:
  * Zawiera klasę, która `IHostingStartup` zawiera implementację.
  * Zawiera [atrybut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do `IHostingStartup` identyfikowania klasy implementacji.
* Aplikacja konsoli jest publikowana w celu uzyskania zależności uruchamiania hostingu. Konsekwencją publikowania aplikacji konsoli jest, że nieużywane zależności są przycinane z pliku zależności.
* Plik zależności jest modyfikowany w celu ustawienia lokalizacji środowiska wykonawczego zestawu uruchamiania hostingu.
* Zestaw uruchamiania hostingu i jego plik zależności jest umieszczany w magazynie pakietów środowiska wykonawczego. Aby odkryć zestaw uruchamiania hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.

Aplikacja konsoli odwołuje się do pakietu [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut identyfikuje klasę jako `IHostingStartup` implementację do ładowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>i wykonywania podczas tworzenia . W poniższym przykładzie obszar `StartupEnhancement`nazw jest `StartupEnhancementHostingStartup`, a klasa jest:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Klasa implementuje `IHostingStartup`. <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `IHostingStartup.Configure`w zestawie uruchamiania hostingu jest `Startup.Configure` wywoływana przez środowisko wykonawcze przed w kodzie użytkownika, co pozwala kod użytkownika zastąpić dowolną konfigurację zapewnianą przez zestaw uruchamiania hostingu.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Podczas tworzenia `IHostingStartup` projektu plik zależności (*.deps.json* `runtime` ) ustawia lokalizację złożenia w folderze *bin:*

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Wyświetlana jest tylko część pliku. Nazwa zestawu w przykładzie jest `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Konfiguracja zapewniana przez startup hostingu

Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma pierwszeństwo:

1. Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji za pomocą, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację po wykonaniu delegatów aplikacji. Konfiguracja uruchamiania hostingu ma pierwszeństwo przed konfiguracją aplikacji przy użyciu tej metody.
1. Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> aplikacji przy użyciu, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację przed wykonaniem delegatów aplikacji. Wartości konfiguracji aplikacji mają pierwszeństwo przed wartościami dostarczonymi przez startup hostingu przy użyciu tej metody.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Określanie zestawu uruchamiania hostingu

W przypadku uruchamiania hostingu dostarczanego przez bibliotekę klas lub konsolę `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` należy określić nazwę zestawu uruchamiania hostingu w zmiennej środowiskowej. Zmienna środowiskowa jest listą zestawów rozdzielanych średnikami.

Tylko zestawy uruchamiania hostingu `HostingStartup` są skanowane w celu uzyskania atrybutu. Dla przykładowej aplikacji *HostingStartupApp*, aby odkryć startupy hostingu opisane wcześniej, zmienna środowiskowa jest ustawiona na następującą wartość:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Zestaw uruchamiania hostingu można również ustawić przy użyciu ustawienia konfiguracji hosta [zestawy uruchamiania hostingu.](xref:fundamentals/host/web-host#hosting-startup-assemblies)

Gdy istnieje wiele zestawów uruchamiania hostingu, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w którym są wymienione zestawy.

## <a name="activation"></a>Aktywacja

Opcje aktywacji uruchamiania hostingu to:

* [Aktywacja magazynu](#runtime-store) &ndash; środowiska uruchomieniowego nie wymaga odwołania do czasu kompilacji do aktywacji. Przykładowa aplikacja umieszcza zestaw uruchamiania hostingu i zależności plików do folderu, *wdrożenia*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku multimachine. Folder *wdrażania* zawiera również skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby włączyć uruchamianie hostingu.
* Odwołanie do czasu kompilacji wymagane do aktywacji
  * [Pakiet NuGet](#nuget-package)
  * [Folder pojemnika projektu](#project-bin-folder)

### <a name="runtime-store"></a>Magazyn środowiska uruchomieniowego

Implementacja uruchamiania hostingu jest umieszczana w [magazynie środowiska wykonawczego](/dotnet/core/deploying/runtime-store). Odwołanie w czasie kompilacji do zestawu nie jest wymagane przez aplikację rozszerzone.

Po zbudowaniu uruchamiania hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet.](/dotnet/core/tools/dotnet-store)

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

W przykładowej aplikacji (projekt*RuntimeStore)* używane jest następujące polecenie:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

W środowisku uruchomieniowym, aby odnajdywać magazyn środowiska `DOTNET_SHARED_STORE` wykonawczego, lokalizacja magazynu środowiska wykonawczego jest dodawany do zmiennej środowiskowej.

**Modyfikowanie i umieszczanie pliku zależności uruchamiania hostingu**

Aby uaktywnić ulepszenie bez odwołania do pakietu do `additionalDeps`ulepszenia, należy określić dodatkowe zależności do środowiska wykonawczego za pomocą programu . `additionalDeps`pozwala na:

* Rozszerz wykres biblioteki aplikacji, udostępniając zestaw dodatkowych plików *.deps.json* do scalenia z własnym plikiem *deps.json* aplikacji podczas uruchamiania.
* Spraw, aby zestaw uruchamiania hostingu był wykrywalny i ładowalny.

Zalecane podejście do generowania pliku dodatkowych zależności jest:

 1. Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska wykonawczego, do którego odwołuje się poprzednia sekcja.
 1. Usuń odwołanie do manifestu `runtime` z bibliotek i sekcji wynikowego pliku *.deps.json.*

W przykładowym projekcie `store.manifest/1.0.0` właściwość jest `targets` `libraries` usuwana z sekcji i:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Umieść plik *deps.json* w następującej lokalizacji:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`&ndash; Lokalizacja dodana `DOTNET_ADDITIONAL_DEPS` do zmiennej środowiskowej.
* `{SHARED FRAMEWORK NAME}`&ndash; Udostępnione framework wymagane dla tego pliku dodatkowych zależności.
* `{SHARED FRAMEWORK VERSION}`&ndash; Minimalna wersja współdzielonej struktury.
* `{ENHANCEMENT ASSEMBLY NAME}`&ndash; Nazwa zestawu ulepszenia.

W przykładowej aplikacji (projekt*RuntimeStore)* plik dodatkowych zależności jest umieszczany w następującej lokalizacji:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

W środowisku uruchomieniowym, aby odnajdywać lokalizację `DOTNET_ADDITIONAL_DEPS` magazynu środowiska wykonawczego, dodatkowa lokalizacja pliku zależności jest dodawana do zmiennej środowiskowej.

W przykładowej aplikacji (Projekt*RuntimeStore)* tworzenie magazynu środowiska wykonawczego i generowanie pliku dodatkowych zależności odbywa się za pomocą skryptu [programu PowerShell.](/powershell/scripting/powershell-scripting)

Aby zapoznać się z przykładami ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych, zobacz [Używanie wielu środowisk](xref:fundamentals/environments).

**Wdrożenie**

Aby ułatwić wdrażanie uruchamiania hostingu w środowisku wielomatynowym, przykładowa aplikacja tworzy folder *wdrażania* w opublikowanych danych wyjściowych, który zawiera:

* Magazyn środowiska uruchomieniowego uruchamiania hostingu.
* Plik zależności uruchamiania hostingu.
* Skrypt programu PowerShell, który `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`tworzy `DOTNET_SHARED_STORE`lub `DOTNET_ADDITIONAL_DEPS` modyfikuje program , i obsługuje aktywację uruchamiania hostingu. Uruchom skrypt z administracyjnego wiersza polecenia programu PowerShell w systemie wdrażania.

### <a name="nuget-package"></a>Pakiet NuGet

Wzmocnienie uruchamiania hostingu mogą być dostarczane w pakiecie NuGet. Pakiet ma `HostingStartup` atrybut. Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:

* Rozszerzony plik projektu aplikacji sprawia, że odwołanie do pakietu dla uruchamiania hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji). Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*). Takie podejście ma zastosowanie do pakietu zestawu uruchamiania hostingu opublikowanego [w nuget.org](https://www.nuget.org/).
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).

Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska wykonawczego, zobacz następujące tematy:

* [Jak utworzyć pakiet NuGet za pomocą narzędzi wieloplatformowych](/dotnet/core/deploying/creating-nuget-packages)
* [Publikowanie pakietów](/nuget/create-packages/publish-a-package)
* [Magazyn pakietu środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Folder pojemnika projektu

Ulepszenie uruchamiania hostingu może być dostarczone przez zestaw wdrożony w *pojemniku*w ulepszonej aplikacji. Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednego z następujących metod:

* Rozszerzony plik projektu aplikacji sprawia, że odwołanie do zestawu do uruchamiania hostingu (odwołanie w czasie kompilacji). Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*). Takie podejście ma zastosowanie, gdy scenariusz wdrażania wymaga dokonania odwołania w czasie kompilacji do zestawu uruchamiania hostingu (plik*dll)* i przenoszenia zestawu do:
  * Projekt zużywający.
  * Lokalizacja dostępna dla projektu zużywającego.
* Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).
* Podczas kierowania na .NET Framework, zestaw jest wczytywany w kontekście obciążenia domyślnego, co w programie .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:
  * Ścieżka &ndash; podstawowa aplikacji Folder *bin,* w którym znajduje się plik wykonywalny aplikacji (*.exe).*
  * Globalna pamięć podręczna &ndash; zestawów (GAC) GaC przechowuje zestawy współużytkowane przez kilka aplikacji platformy .NET Framework. Aby uzyskać więcej informacji, zobacz [Jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji programu .NET Framework.

## <a name="sample-code"></a>Przykładowy kod

[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)demonstruje scenariusze implementacji uruchamiania hostingu:

* Dwa zestawy uruchamiania hostingu (biblioteki klas) ustawiają parę par klucza-wartości konfiguracji w pamięci:
  * Pakiet NuGet (*HostingStartupPackage*)
  * Biblioteka klas (*HostingStartupLibrary*)
* Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w magazynie uruchomieniowym *(StartupDiagnostics*). Zestaw dodaje dwa oprogramowanie pośredniczące do aplikacji podczas uruchamiania, które zawierają informacje diagnostyczne na:
  * Zarejestrowane usługi
  * Adres (schemat, host, podstawa ścieżki, ścieżka, ciąg zapytania)
  * Połączenie (zdalny adres IP, port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)
  * Nagłówki żądań
  * Zmienne środowiskowe

Aby uruchomić próbkę:

**Aktywacja z pakietu NuGet**

1. Skompiluj pakiet *HostingStartupPackage* za pomocą polecenia [dotnet pack.](/dotnet/core/tools/dotnet-pack)
1. Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.
1. Skompiluj i uruchom aplikację. Odwołanie do pakietu jest obecny w aplikacji rozszerzonej (odwołanie w czasie kompilacji). A `<PropertyGroup>` w pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/debug)* jako źródło pakietu. Dzięki temu aplikacja może korzystać z pakietu bez przesyłania pakietu do [nuget.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę pakietu.

Jeśli wniesiesz zmiany do projektu *HostingStartupPackage* i ponownie skompilować go, wyczyść lokalne pamięci podręczne pakietu NuGet, aby upewnić się, że *HostingStartupApp* odbiera zaktualizowany pakiet, a nie nieaktualny pakiet z lokalnej pamięci podręcznej. Aby wyczyścić lokalne bufory NuGet, wykonaj następujące polecenie [dotnet nuget locals:](/dotnet/core/tools/dotnet-nuget-locals)

```dotnetcli
dotnet nuget locals all --clear
```

**Aktywacja z biblioteki klas**

1. Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [dotnet build.](/dotnet/core/tools/dotnet-build)
1. Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`
1. *bin*-deploy zestawu biblioteki klas do aplikacji przez skopiowanie *pliku HostingStartupLibrary.dll* z biblioteki klas skompilowanych danych wyjściowych do folderu *bin/debug* aplikacji.
1. Skompiluj i uruchom aplikację. Plik `<ItemGroup>` projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odwołanie do czasu kompilacji). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę biblioteki klas.

**Aktywacja z zestawu wdrożonego w magazynie uruchomieniowym**

1. Projekt *StartupDiagnostics* używa programu [PowerShell](/powershell/scripting/powershell-scripting) do modyfikowania pliku *StartupDiagnostics.deps.json.* Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatku SP1 i dodatku SP1 dla systemu Windows Server 2008 R2. Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).
1. Wykonaj skrypt *build.ps1* w folderze *RuntimeStore.* Skrypt:
   * Generuje `StartupDiagnostics` pakiet w folderze *obj\packages.*
   * Generuje magazyn środowiska wykonawczego `StartupDiagnostics` w folderze *magazynu.* Polecenie `dotnet store` w skrypcie `win7-x64` używa [identyfikatora środowiska wykonawczego (RID)](/dotnet/core/rid-catalog) dla uruchamiania hostingu wdrożonego w systemie Windows. Podczas dostarczania uruchamiania hostingu dla innego środowiska uruchomieniowego, zastąp poprawny rid w wierszu 37 skryptu. Magazyn środowiska wykonawczego `StartupDiagnostics` zostanie później przeniesiony do magazynu środowiska wykonawczego użytkownika lub systemu na komputerze, na którym zostanie wykorzystany zestaw. Lokalizacja instalacji magazynu uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.
   * Generuje for `additionalDeps` `StartupDiagnostics` w *folderze additionalDeps.* Dodatkowe zależności zostaną później przeniesione do dodatkowych zależności użytkownika lub systemu. Lokalizacją `StartupDiagnostics` instalacji dodatkowych zależności użytkownika jest *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.
   * Umieszcza plik *deploy.ps1* w folderze *wdrażania.*
1. Uruchom skrypt *deploy.ps1* w folderze *wdrażania.* Skrypt dołącza:
   * `StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.
   * Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu `DOTNET_ADDITIONAL_DEPS` RuntimeStore) do zmiennej środowiskowej.
   * Ścieżka magazynu środowiska wykonawczego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej. `DOTNET_SHARED_STORE`
1. Uruchom przykładową aplikację.
1. Poproś `/services` o punkt końcowy, aby wyświetlić zarejestrowane usługi aplikacji. Poproś `/diag` o punkt końcowy, aby wyświetlić informacje diagnostyczne.

::: moniker-end
