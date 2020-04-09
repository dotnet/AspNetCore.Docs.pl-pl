---
title: Wykrywanie zmian za pomocą tokenów zmian w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać tokenów zmian do śledzenia zmian.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656346"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Wykrywanie zmian za pomocą tokenów zmian w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

*Token zmiany* jest uniwersalnym, niskopoziomowym blokiem konstrukcyjnym używanym do śledzenia zmian stanu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interfejs IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany. `IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw. [Pakiet Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet jest niejawnie dostarczany do aplikacji ASP.NET Core.

`IChangeToken`posiada dwie właściwości:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskazać, czy token proaktywnie wywołuje wywołania zwrotne. Jeśli `ActiveChangedCallbacks` jest `false`ustawiona na , wywołanie zwrotne `HasChanged` nigdy nie jest wywoływane, a aplikacja musi sondować zmiany. Jest również możliwe dla tokenu nigdy nie zostać anulowane, jeśli nie nastąpią żadne zmiany lub odbiornika zmian podstawowych jest usuwany lub wyłączony.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>otrzymuje wartość, która wskazuje, czy nastąpiła zmiana.

Interfejs `IChangeToken` zawiera [RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) który rejestruje wywołanie zwrotne, który jest wywoływany po zmianie tokenu. `HasChanged`należy ustawić przed wywołaniem wywołania zwrotnego.

## <a name="changetoken-class"></a>ChangeToken, klasa

<xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną używaną do propagowania powiadomień o wystąpieniu zmiany. `ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw. [Pakiet Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet jest niejawnie dostarczany do aplikacji ASP.NET Core.

[ChangeToken.OnChange(Func\<IChangeToken>, akcja)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywołać do każdej zmiany tokenu:

* `Func<IChangeToken>`tworzy żeton.
* `Action`jest wywoływana, gdy zmienia się token.

Przeciążenie [\<ChangeToken.OnChange TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest `Action`przekazywany do konsumenta tokenu .

`OnChange`zwraca <xref:System.IDisposable>plik . Wywołanie <xref:System.IDisposable.Dispose*> zatrzymuje tokenu od nasłuchiwania dalszych zmian i zwalnia zasoby tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Przykładowe zastosowania tokenów zmian w ASP.NET Core

Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian w obiektach:

* Do monitorowania zmian <xref:Microsoft.Extensions.FileProviders.IFileProvider>w <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> plikach, `IChangeToken` 's metoda tworzy dla określonych plików lub folderu do oglądania.
* `IChangeToken`tokeny mogą być dodawane do wpisów pamięci podręcznej, aby wyzwolić eksmisje pamięci podręcznej na zmiany.
* W `TOptions` przypadku zmian <xref:Microsoft.Extensions.Options.OptionsMonitor`1> domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma przeciążenie, które akceptuje jedno lub więcej <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpień. Każde wystąpienie `IChangeToken` zwraca, aby zarejestrować wywołanie zwrotne powiadomienia o zmianie dla zmian opcji śledzenia.

## <a name="monitor-for-configuration-changes"></a>Monitoruj zmiany konfiguracji

Domyślnie ASP.NET szablonów Core używają [plików konfiguracyjnych JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*i *appsettings. Production.json*), aby załadować ustawienia konfiguracji aplikacji.

Pliki te są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr. `reloadOnChange`wskazuje, czy konfiguracja powinna zostać ponownie załadowana przy zmianach w pliku. To ustawienie pojawia <xref:Microsoft.Extensions.Hosting.Host> się <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>w metodzie wygody:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfiguracja oparta na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>plikach jest reprezentowana przez program . `FileConfigurationSource`do <xref:Microsoft.Extensions.FileProviders.IFileProvider> monitorowania plików.

Domyślnie `IFileMonitor` jest dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>program , <xref:System.IO.FileSystemWatcher> który służy do monitorowania zmian w pliku konfiguracyjnym.

Przykładowa aplikacja demonstruje dwie implementacje do monitorowania zmian konfiguracji. Jeśli którykolwiek z *appsettings* plików zmiany, zarówno implementacje monitorowania plików wykonać kod&mdash;niestandardowy przykładowy aplikacja zapisuje komunikat do konsoli.

Plik konfiguracyjny `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenu dla zmiany pliku pojedynczej konfiguracji. Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz po wyzwoleniu wielu wywołań zwrotnych tokenu, implementacja przykładu sprawdza skróty plików. W przykładzie użyto mieszania plików SHA1. Ponowna próba jest implementowana z wykładniczym wycofywania. Ponowna próby jest obecny, ponieważ może wystąpić blokowanie plików, które tymczasowo uniemożliwia obliczanie nowego skrótu w pliku.

*Narzędzia/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Prosty token zmiany uruchamiania

Zarejestruj wywołania `Action` zwrotne konsumenta tokenu dla powiadomień o zmianie do tokenu ponownego ładowania konfiguracji.

W pliku `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`zapewnia token. Wywołanie zwrotne `InvokeChanged` jest metodą:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Wywołanie zwrotne jest używany do przekazywania `state` w `IWebHostEnvironment`programie , który jest przydatny do określenia pliku konfiguracji poprawne *appsettings* do monitorowania (na przykład *appsettings. Development.json,* gdy w środowisku programistycznym). Skróty plików są używane, `WriteConsole` aby zapobiec wiele razy uruchamianie instrukcji z powodu wielu wywołań zwrotnych tokenu, gdy plik konfiguracji zmienił się tylko raz.

Ten system działa tak długo, jak aplikacja jest uruchomiona i nie może być wyłączona przez użytkownika.

### <a name="monitor-configuration-changes-as-a-service"></a>Monitorowanie zmian konfiguracji jako usługi

Przykład implementuje:

* Podstawowe monitorowanie tokenu uruchamiania.
* Monitorowanie jako usługa.
* Mechanizm włączania i wyłączania monitorowania.

Próbka ustanawia `IConfigurationMonitor` interfejs.

*Rozszerzenia/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor zaimplementowana `ConfigurationMonitor`klasa, rejestruje wywołanie zwrotne dla powiadomień o zmianie:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dostarcza żeton. `InvokeChanged`jest metodą wywołania zwrotnego. W `state` tym przypadku jest odwołanie `IConfigurationMonitor` do wystąpienia, który jest używany do dostępu do stanu monitorowania. Używane są dwie właściwości:

* `MonitoringEnabled`&ndash; Wskazuje, czy wywołanie zwrotne należy uruchomić jego kod niestandardowy.
* `CurrentState`&ndash; Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.

Metoda `InvokeChanged` jest podobna do wcześniejszego podejścia, z tą różnicą, że:

* Nie uruchamia swojego kodu, chyba że `MonitoringEnabled` jest `true`.
* Wyprowadza prąd `state` w `WriteConsole` jego wyjściu.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Wystąpienie `ConfigurationMonitor` jest rejestrowane jako `Startup.ConfigureServices`usługa w:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Strona Indeks oferuje kontrolę użytkownika nad monitorowaniem konfiguracji. Wystąpienie `IConfigurationMonitor` jest wstrzykiwane `IndexModel`do pliku .

*Strony/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Monitor konfiguracji`_monitor`( ) służy do włączania lub wyłączania monitorowania i ustawiania bieżącego stanu sprzężenia zwrotnego interfejsu użytkownika:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Po `OnPostStartMonitoring` wyzwoleniu monitorowanie jest włączone, a bieżący stan jest czyszczony. Po `OnPostStopMonitoring` wyzwoleniu monitorowanie jest wyłączone, a stan jest ustawiony tak, aby odzwierciedlać, że monitorowanie nie występuje.

Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.

*Strony/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorowanie zmian w buforowanym pliku

Zawartość pliku można buforować w <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>pamięci za pomocą programu . Buforowanie w pamięci jest opisane w [pamięci podręcznej w pamięci](xref:performance/caching/memory) tematu. Bez podejmowania dodatkowych kroków, takich jak implementacja opisana poniżej, *przestarzałe* (nieaktualne) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe ulegną zmianie.

Na przykład nie biorąc pod uwagę stan buforowanego pliku źródłowego podczas [odnawiania okres wygaśnięcia przesuwne](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych pliku w pamięci podręcznej. Każde żądanie danych odnawia przesuwany okres wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej. Wszystkie funkcje aplikacji, które używają zawartości buforowanej pliku, mogą odbierać nieaktualną zawartość.

Za pomocą zmiany tokenów w scenariuszu buforowania plików zapobiega obecności starych zawartości pliku w pamięci podręcznej. Przykładowa aplikacja demonstruje implementację podejścia.

Próbka wykorzystuje `GetFileContent` do:

* Zwracanie zawartości pliku.
* Zaimplementuj algorytm ponawiania próby z wykładniczym wycofywania w celu pokrycia przypadków, w których blokada pliku tymczasowo uniemożliwia odczyt pliku.

*Narzędzia/Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

A `FileService` jest tworzony do obsługi wyszukiwania plików w pamięci podręcznej. Wywołanie `GetFileContent` metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci podręcznej i zwrócić ją do osoby dzwoniącej (*Services/FileService.cs*).

Jeśli zawartość w pamięci podręcznej nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną podjęte następujące akcje:

1. Zawartość pliku jest `GetFileContent`uzyskiwana za pomocą programu .
1. Token zmiany jest uzyskiwany od dostawcy plików za pomocą [pliku IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Wywołania zwrotnego tokenu jest wyzwalany, gdy plik jest modyfikowany.
1. Zawartość pliku jest buforowana z przesuwanym okresem [wygaśnięcia.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) Token zmiany jest dołączony do [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) do eksmisji wpis pamięci podręcznej, jeśli plik zmienia się, gdy jest buforowany.

W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji . `IWebHostEnvironment.ContentRootFileProvider`służy do uzyskania <xref:Microsoft.Extensions.FileProviders.IFileProvider> punktu na aplikacji `IWebHostEnvironment.ContentRootPath`. Jest `filePath` uzyskiwany za pomocą [pliku IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

Jest `FileService` zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.

W pliku `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Model strony ładuje zawartość pliku przy użyciu usługi.

W `OnGet` metodzie strony Indeks *(Pages/Index.cshtml.cs):*

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken, klasa

Aby reprezentować jedno `IChangeToken` lub więcej wystąpień w <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> jednym obiekcie, należy użyć klasy.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `HasChanged` . `ActiveChangeCallbacks`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `ActiveChangeCallbacks` . Jeśli wystąpi wiele zdarzeń zmiany równoczesnych, wywołania zwrotnego zmiany złożonej jest wywoływana jeden raz.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Token zmiany* jest uniwersalnym, niskopoziomowym blokiem konstrukcyjnym używanym do śledzenia zmian stanu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Interfejs IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>propaguje powiadomienia o wystąpieniu zmiany. `IChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw. W przypadku aplikacji, które nie używają [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)utwórz odwołanie do pakietu [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.

`IChangeToken`posiada dwie właściwości:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>wskazać, czy token proaktywnie wywołuje wywołania zwrotne. Jeśli `ActiveChangedCallbacks` jest `false`ustawiona na , wywołanie zwrotne `HasChanged` nigdy nie jest wywoływane, a aplikacja musi sondować zmiany. Jest również możliwe dla tokenu nigdy nie zostać anulowane, jeśli nie nastąpią żadne zmiany lub odbiornika zmian podstawowych jest usuwany lub wyłączony.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>otrzymuje wartość, która wskazuje, czy nastąpiła zmiana.

Interfejs `IChangeToken` zawiera [RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) który rejestruje wywołanie zwrotne, który jest wywoływany po zmianie tokenu. `HasChanged`należy ustawić przed wywołaniem wywołania zwrotnego.

## <a name="changetoken-class"></a>ChangeToken, klasa

<xref:Microsoft.Extensions.Primitives.ChangeToken>jest klasą statyczną używaną do propagowania powiadomień o wystąpieniu zmiany. `ChangeToken`znajduje się w <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> obszarze nazw. W przypadku aplikacji, które nie używają [metapakietu Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)utwórz odwołanie do pakietu [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.

[ChangeToken.OnChange(Func\<IChangeToken>, akcja)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) rejestruje `Action` wywołać do każdej zmiany tokenu:

* `Func<IChangeToken>`tworzy żeton.
* `Action`jest wywoływana, gdy zmienia się token.

Przeciążenie [\<ChangeToken.OnChange TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) przyjmuje dodatkowy `TState` parametr, który jest `Action`przekazywany do konsumenta tokenu .

`OnChange`zwraca <xref:System.IDisposable>plik . Wywołanie <xref:System.IDisposable.Dispose*> zatrzymuje tokenu od nasłuchiwania dalszych zmian i zwalnia zasoby tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Przykładowe zastosowania tokenów zmian w ASP.NET Core

Tokeny zmiany są używane w widocznych obszarach ASP.NET Core do monitorowania zmian w obiektach:

* Do monitorowania zmian <xref:Microsoft.Extensions.FileProviders.IFileProvider>w <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> plikach, `IChangeToken` 's metoda tworzy dla określonych plików lub folderu do oglądania.
* `IChangeToken`tokeny mogą być dodawane do wpisów pamięci podręcznej, aby wyzwolić eksmisje pamięci podręcznej na zmiany.
* W `TOptions` przypadku zmian <xref:Microsoft.Extensions.Options.OptionsMonitor`1> domyślna implementacja <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> ma przeciążenie, które akceptuje jedno lub więcej <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> wystąpień. Każde wystąpienie `IChangeToken` zwraca, aby zarejestrować wywołanie zwrotne powiadomienia o zmianie dla zmian opcji śledzenia.

## <a name="monitor-for-configuration-changes"></a>Monitoruj zmiany konfiguracji

Domyślnie ASP.NET szablonów Core używają [plików konfiguracyjnych JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*i *appsettings. Production.json*), aby załadować ustawienia konfiguracji aplikacji.

Pliki te są konfigurowane przy użyciu metody rozszerzenia [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> która akceptuje `reloadOnChange` parametr. `reloadOnChange`wskazuje, czy konfiguracja powinna zostać ponownie załadowana przy zmianach w pliku. To ustawienie pojawia <xref:Microsoft.AspNetCore.WebHost> się <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>w metodzie wygody:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfiguracja oparta na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>plikach jest reprezentowana przez program . `FileConfigurationSource`do <xref:Microsoft.Extensions.FileProviders.IFileProvider> monitorowania plików.

Domyślnie `IFileMonitor` jest dostarczany przez <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>program , <xref:System.IO.FileSystemWatcher> który służy do monitorowania zmian w pliku konfiguracyjnym.

Przykładowa aplikacja demonstruje dwie implementacje do monitorowania zmian konfiguracji. Jeśli którykolwiek z *appsettings* plików zmiany, zarówno implementacje monitorowania plików wykonać kod&mdash;niestandardowy przykładowy aplikacja zapisuje komunikat do konsoli.

Plik konfiguracyjny `FileSystemWatcher` może wyzwolić wiele wywołań zwrotnych tokenu dla zmiany pliku pojedynczej konfiguracji. Aby upewnić się, że kod niestandardowy jest uruchamiany tylko raz po wyzwoleniu wielu wywołań zwrotnych tokenu, implementacja przykładu sprawdza skróty plików. W przykładzie użyto mieszania plików SHA1. Ponowna próba jest implementowana z wykładniczym wycofywania. Ponowna próby jest obecny, ponieważ może wystąpić blokowanie plików, które tymczasowo uniemożliwia obliczanie nowego skrótu w pliku.

*Narzędzia/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Prosty token zmiany uruchamiania

Zarejestruj wywołania `Action` zwrotne konsumenta tokenu dla powiadomień o zmianie do tokenu ponownego ładowania konfiguracji.

W pliku `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`zapewnia token. Wywołanie zwrotne `InvokeChanged` jest metodą:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Wywołanie zwrotne jest używany do przekazywania `state` w `IHostingEnvironment`programie , który jest przydatny do określenia pliku konfiguracji poprawne *appsettings* do monitorowania (na przykład *appsettings. Development.json,* gdy w środowisku programistycznym). Skróty plików są używane, `WriteConsole` aby zapobiec wiele razy uruchamianie instrukcji z powodu wielu wywołań zwrotnych tokenu, gdy plik konfiguracji zmienił się tylko raz.

Ten system działa tak długo, jak aplikacja jest uruchomiona i nie może być wyłączona przez użytkownika.

### <a name="monitor-configuration-changes-as-a-service"></a>Monitorowanie zmian konfiguracji jako usługi

Przykład implementuje:

* Podstawowe monitorowanie tokenu uruchamiania.
* Monitorowanie jako usługa.
* Mechanizm włączania i wyłączania monitorowania.

Próbka ustanawia `IConfigurationMonitor` interfejs.

*Rozszerzenia/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor zaimplementowana `ConfigurationMonitor`klasa, rejestruje wywołanie zwrotne dla powiadomień o zmianie:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dostarcza żeton. `InvokeChanged`jest metodą wywołania zwrotnego. W `state` tym przypadku jest odwołanie `IConfigurationMonitor` do wystąpienia, który jest używany do dostępu do stanu monitorowania. Używane są dwie właściwości:

* `MonitoringEnabled`&ndash; Wskazuje, czy wywołanie zwrotne należy uruchomić jego kod niestandardowy.
* `CurrentState`&ndash; Opisuje bieżący stan monitorowania do użycia w interfejsie użytkownika.

Metoda `InvokeChanged` jest podobna do wcześniejszego podejścia, z tą różnicą, że:

* Nie uruchamia swojego kodu, chyba że `MonitoringEnabled` jest `true`.
* Wyprowadza prąd `state` w `WriteConsole` jego wyjściu.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Wystąpienie `ConfigurationMonitor` jest rejestrowane jako `Startup.ConfigureServices`usługa w:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Strona Indeks oferuje kontrolę użytkownika nad monitorowaniem konfiguracji. Wystąpienie `IConfigurationMonitor` jest wstrzykiwane `IndexModel`do pliku .

*Strony/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Monitor konfiguracji`_monitor`( ) służy do włączania lub wyłączania monitorowania i ustawiania bieżącego stanu sprzężenia zwrotnego interfejsu użytkownika:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Po `OnPostStartMonitoring` wyzwoleniu monitorowanie jest włączone, a bieżący stan jest czyszczony. Po `OnPostStopMonitoring` wyzwoleniu monitorowanie jest wyłączone, a stan jest ustawiony tak, aby odzwierciedlać, że monitorowanie nie występuje.

Przyciski w interfejsie użytkownika włączają i wyłączają monitorowanie.

*Strony/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorowanie zmian w buforowanym pliku

Zawartość pliku można buforować w <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>pamięci za pomocą programu . Buforowanie w pamięci jest opisane w [pamięci podręcznej w pamięci](xref:performance/caching/memory) tematu. Bez podejmowania dodatkowych kroków, takich jak implementacja opisana poniżej, *przestarzałe* (nieaktualne) dane są zwracane z pamięci podręcznej, jeśli dane źródłowe ulegną zmianie.

Na przykład nie biorąc pod uwagę stan buforowanego pliku źródłowego podczas [odnawiania okres wygaśnięcia przesuwne](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) prowadzi do starych danych pliku w pamięci podręcznej. Każde żądanie danych odnawia przesuwany okres wygaśnięcia, ale plik nigdy nie jest ponownie ładowany do pamięci podręcznej. Wszystkie funkcje aplikacji, które używają zawartości buforowanej pliku, mogą odbierać nieaktualną zawartość.

Za pomocą zmiany tokenów w scenariuszu buforowania plików zapobiega obecności starych zawartości pliku w pamięci podręcznej. Przykładowa aplikacja demonstruje implementację podejścia.

Próbka wykorzystuje `GetFileContent` do:

* Zwracanie zawartości pliku.
* Zaimplementuj algorytm ponawiania próby z wykładniczym wycofywania w celu pokrycia przypadków, w których blokada pliku tymczasowo uniemożliwia odczyt pliku.

*Narzędzia/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

A `FileService` jest tworzony do obsługi wyszukiwania plików w pamięci podręcznej. Wywołanie `GetFileContent` metody usługi próbuje uzyskać zawartość pliku z pamięci podręcznej w pamięci podręcznej i zwrócić ją do osoby dzwoniącej (*Services/FileService.cs*).

Jeśli zawartość w pamięci podręcznej nie zostanie znaleziona przy użyciu klucza pamięci podręcznej, zostaną podjęte następujące akcje:

1. Zawartość pliku jest `GetFileContent`uzyskiwana za pomocą programu .
1. Token zmiany jest uzyskiwany od dostawcy plików za pomocą [pliku IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Wywołania zwrotnego tokenu jest wyzwalany, gdy plik jest modyfikowany.
1. Zawartość pliku jest buforowana z przesuwanym okresem [wygaśnięcia.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) Token zmiany jest dołączony do [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) do eksmisji wpis pamięci podręcznej, jeśli plik zmienia się, gdy jest buforowany.

W poniższym przykładzie pliki są przechowywane w [katalogu głównym zawartości](xref:fundamentals/index#content-root)aplikacji . [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) służy do <xref:Microsoft.Extensions.FileProviders.IFileProvider> uzyskania wskazując na aplikacji <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>. Jest `filePath` uzyskiwany za pomocą [pliku IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

Jest `FileService` zarejestrowany w kontenerze usługi wraz z usługą buforowania pamięci.

W pliku `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model strony ładuje zawartość pliku przy użyciu usługi.

W `OnGet` metodzie strony Indeks *(Pages/Index.cshtml.cs):*

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken, klasa

Aby reprezentować jedno `IChangeToken` lub więcej wystąpień w <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> jednym obiekcie, należy użyć klasy.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `HasChanged` . `ActiveChangeCallbacks`w raportach `true` tokenu złożonego, `true`jeśli jest reprezentowany token `ActiveChangeCallbacks` . Jeśli wystąpi wiele zdarzeń zmiany równoczesnych, wywołania zwrotnego zmiany złożonej jest wywoływana jeden raz.

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
