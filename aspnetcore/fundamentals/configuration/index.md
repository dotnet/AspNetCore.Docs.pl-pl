---
title: Konfiguracja w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core za pomocą interfejsu API konfiguracji.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 506f01ace72d6e915c0f3ebdaae5b4a3328a79b9
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007161"
---
# <a name="configuration-in-aspnet-core"></a>Konfiguracja w ASP.NET Core

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Kirk [Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Konfiguracja w ASP.NET Core jest wykonywana przy użyciu co najmniej jednego [dostawcy konfiguracji](#cp). Dostawcy konfiguracji odczytywać dane konfiguracyjne z par klucz-wartość przy użyciu różnych źródeł konfiguracji:

* Pliki ustawień, takie jak *appsettings.json*
* Zmienne środowiskowe
* W usłudze Azure Key Vault
* Azure App Configuration
* Argumenty wiersza polecenia
* Dostawcy niestandardowi, zainstalowane lub utworzone
* Pliki katalogów
* Obiekty NET w pamięci

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Konfiguracja domyślna

ASP.NET aplikacje sieci Web Core utworzone za pomocą [dotnet new](/dotnet/core/tools/dotnet-new) lub Visual Studio generują następujący kod:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zapewnia domyślną konfigurację aplikacji w następującej kolejności:

1. [ChainedConfigurationProvider:](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) Dodaje `IConfiguration` istniejące jako źródło. W domyślnej obudowie konfiguracji dodaje konfigurację [hosta](#hvac) i ustawia ją jako pierwsze źródło konfiguracji _aplikacji._
1. [appsettings.json](#appsettingsjson) przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider).
1. *appsettings.* `Environment` *.json* przy użyciu [dostawcy konfiguracji JSON](#file-configuration-provider). Na przykład *appsettings*. ***Produkcja***. *json* i *appsettings*. ***Rozwoju***. *json*.
1. [Wpisy tajne aplikacji,](xref:security/app-secrets) `Development` gdy aplikacja działa w środowisku.
1. Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmienne środowiskowe](#evcp).
1. Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line).

Dostawcy konfiguracji, którzy są dodani później, zastępują poprzednie ustawienia klucza. Na przykład `MyKey` jeśli jest ustawiona w obu *appsettings.json* i środowiska, wartość środowiska jest używana. Korzystając z domyślnych dostawców konfiguracji, [dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) zastępuje wszystkich innych dostawców.

Aby uzyskać `CreateDefaultBuilder`więcej informacji na temat , zobacz [Domyślne ustawienia konstruktora](xref:fundamentals/host/generic-host#default-builder-settings).

Następujący kod wyświetla włączonych dostawców konfiguracji w kolejności, w jakiej zostały dodane:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.json

Należy wziąć pod uwagę następujący plik *appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Domyślna <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> konfiguracja ładuje w następującej kolejności:

1. *appsettings.json*
1. *appsettings.* `Environment` *.json* : Na przykład *appsettings*. ***Produkcja***. *json* i *appsettings*. ***Rozwoju***. *json* plików. Wersja środowiska pliku jest ładowana na podstawie [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*). Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

*appsettings*. `Environment`. *json* values override keys in *appsettings.json*. Na przykład domyślnie:

* W rozwoju, *appsettings*. ***Rozwoju***. *konfiguracja json* zastępuje wartości znalezione w *pliku appsettings.json*.
* W *produkcji, appsettings*. ***Produkcja***. *konfiguracja json* zastępuje wartości znalezione w *pliku appsettings.json*. Na przykład podczas wdrażania aplikacji na platformie Azure.

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Powiązanie hierarchicznych danych konfiguracyjnych przy użyciu wzorca opcji

Preferowanym sposobem odczytywania powiązanych wartości konfiguracyjnych jest użycie [wzorca opcji](xref:fundamentals/configuration/options). Na przykład, aby odczytać następujące wartości konfiguracji:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Utwórz `PositionOptions` następującą klasę:

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Wszystkie publiczne właściwości odczytu i zapisu typu są powiązane. Pola ***nie*** są powiązane.

Następujący kod:

* Wywołuje [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) do `PositionOptions` powiązania `Position` klasy z sekcją.
* Wyświetla `Position` dane konfiguracyjne.

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)wiąże i zwraca określony typ. `ConfigurationBinder.Get<T>`może być wygodniejsza `ConfigurationBinder.Bind`niż korzystanie z programu . Poniższy kod pokazuje, `ConfigurationBinder.Get<T>` jak `PositionOptions` używać z klasą:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Alternatywnym podejściem podczas korzystania z `Position` ***wzorca opcji*** jest powiązanie sekcji i dodanie jej do [kontenera usługi iniekcji zależności.](xref:fundamentals/dependency-injection) W poniższym `PositionOptions` kodzie jest dodawany <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> do kontenera usługi z konfiguracją i powiązany z nią:

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Przy użyciu poprzedniego kodu, następujący kod odczytuje opcje pozycji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Przy użyciu [domyślnej](#default) konfiguracji, *appsettings.json* i *appsettings.* `Environment` *Pliki .json* są włączone z [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75). Zmiany wprowadzone w *appsettings.json* i *appsettings.* `Environment` *.json* ***po*** uruchomieniu aplikacji są odczytywane przez [dostawcę konfiguracji JSON](#jcp).

Zobacz [dostawcę konfiguracji JSON](#jcp) w tym dokumencie, aby uzyskać informacje na temat dodawania dodatkowych plików konfiguracyjnych JSON.

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Bezpieczeństwo i tajny menedżer

Wskazówki dotyczące danych konfiguracyjnych:

* Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie dostawcy konfiguracji lub w plikach konfiguracyjnych w postaci zwykłego tekstu. [Menedżer secret](xref:security/app-secrets) może służyć do przechowywania wpisów tajnych w rozwoju.
* Nie używaj wpisów tajnych produkcji w środowiskach deweloperskich lub testowych.
* Określ wpisy tajne poza projektem, tak aby nie można przypadkowo zatwierdzone do repozytorium kodu źródłowego.

[Domyślnie](#default) [menedżer secret](xref:security/app-secrets) odczytuje ustawienia konfiguracji po *appsettings.json* i *appsettings.* `Environment` *.json*.

Aby uzyskać więcej informacji na temat przechowywania haseł lub innych poufnych danych:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych. Menedżer tajny używa [dostawcy konfiguracji pliku](#fcp) do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym.

[Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpiecznie przechowuje wpisy tajne aplikacji dla aplikacji ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Zmienne środowiskowe

Przy użyciu konfiguracji <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> [domyślnej,](#default) konfiguracja ładuje z pary klucza-wartość zmiennej środowiskowej po *przeczytaniu appsettings.json*, *appsettings.* `Environment` *.json*i [Secret manager](xref:security/app-secrets). W związku z tym kluczowe wartości odczytywane ze środowiska zastępują wartości odczytywane z *pliku appsettings.json*, *appsettings.* `Environment` *.json*i secret manager.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Następujące `set` polecenia:

* Ustaw klucze środowiska i wartości [poprzedniego przykładu](#appsettingsjson) w systemie Windows.
* Przetestuj ustawienia podczas korzystania z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). Polecenie `dotnet run` musi być uruchomione w katalogu projektu.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Poprzednie ustawienia środowiska:

* Są ustawiane tylko w procesach uruchomionych z okna poleceń, w które zostały ustawione.
* Nie będą odczytywane przez przeglądarki uruchomione za pomocą programu Visual Studio.

Następujące polecenia [setx](/windows-server/administration/windows-commands/setx) mogą służyć do ustawiania kluczy i wartości środowiska w systemie Windows. W `set` `setx` przeciwieństwie do , ustawienia są zachowywane. `/M`ustawia zmienną w środowisku systemowym. Jeśli `/M` przełącznik nie jest używany, ustawiona jest zmienna środowiskowa użytkownika.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Aby sprawdzić, czy poprzednie polecenia zastępują *appsettings.json* i *appsettings.* `Environment` *.json*:

* Z visual studio: Exit i restart Visual Studio.
* Z interfejsu wiersza polecenia: Uruchom `dotnet run`nowe okno polecenia i wprowadź .

Wywołanie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> ciągiem, aby określić prefiks dla zmiennych środowiskowych:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

Powyższy kod ma następujące działanie:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`jest dodawany po [domyślnych dostawcach konfiguracji](#default). Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).
* Zmienne środowiskowe `MyCustomPrefix_` ustawione z prefiksem zastępują [domyślnych dostawców konfiguracji](#default). Obejmuje to zmienne środowiskowe bez prefiksu.

Prefiks jest usuwany po odczytaniu par klucza konfiguracji-wartość.

Następujące polecenia testują prefiks niestandardowy:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

[Domyślna konfiguracja](#default) ładuje zmienne środowiskowe i `DOTNET_` `ASPNETCORE_`argumenty wiersza polecenia poprzedzone i . Prefiksy `DOTNET_` i `ASPNETCORE_` są używane przez ASP.NET Core dla konfiguracji [hosta i aplikacji,](xref:fundamentals/host/generic-host#host-configuration)ale nie dla konfiguracji użytkownika. Aby uzyskać więcej informacji na temat konfiguracji hosta i aplikacji, zobacz [Host ogólny .NET](xref:fundamentals/host/generic-host).

W [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję Nowe ustawienie **aplikacji** na stronie Ustawienia **> konfiguracja.** Ustawienia aplikacji usługi Azure App Service to:

* Szyfrowane w spoczynku i przesyłane za pośrednictwem zaszyfrowanego kanału.
* Narażone jako zmienne środowiskowe.

Aby uzyskać więcej informacji, zobacz [Usługi Azure Apps: Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Zobacz [Prefiksy ciągu połączenia,](#constr) aby uzyskać informacje na temat ciągów połączeń bazy danych platformy Azure.

<a name="clcp"></a>

## <a name="command-line"></a>Wiersz polecenia

Przy użyciu konfiguracji <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> [domyślnej](#default) konfiguracja ładuje z argumentu wiersza polecenia pary klucz-wartość po następujących źródłach konfiguracji:

* *appsettings.json* i *appsettings*. `Environment`. *json* plików.
* [Wpisy tajne aplikacji (Secret Manager)](xref:security/app-secrets) w środowisku deweloperskim.
* Zmienne środowiskowe.

[Domyślnie](#default)wartości konfiguracyjne ustawione w wierszu polecenia zastępują wartości konfiguracji ustawione dla wszystkich innych dostawców konfiguracji.

### <a name="command-line-arguments"></a>Argumenty wiersza polecenia

Następujące polecenie ustawia klucze `=`i wartości za pomocą:

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Następujące polecenie ustawia klucze `/`i wartości za pomocą:

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Następujące polecenie ustawia klucze `--`i wartości za pomocą:

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Wartość klucza:

* Musi `=`następować , lub klucz `--` musi `/` mieć prefiks lub gdy wartość następuje spacji.
* Nie jest wymagane, `=` jeśli jest używany. Na przykład `MySetting=`.

W ramach tego samego polecenia nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają `=` par klucz-wartość, które używają spacji.

### <a name="switch-mappings"></a>Mapowania przełączników

Mapowania przełączników umożliwiają logikę zastępowania nazw **kluczy.** Podaj słownik zamienników przełącznika do <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> metody.

Gdy używany jest słownik mapowania przełącznika, słownik jest sprawdzany pod kątem klucza zgodnego z kluczem dostarczonym przez argument wiersza polecenia. Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika jest przekazywana z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji. Mapowanie przełącznika jest wymagane dla każdego klucza wiersza`-`polecenia poprzedzonym pojedynczą kreską ( ).

Przełącz reguły klucza słownika mapowania:

* Przełączniki muszą `-` zaczynać się od lub `--`.
* Słownik mapowań przełączników nie może zawierać zduplikowanych kluczy.

Aby użyć słownika mapowań przełączników, należy `AddCommandLine`przekazać go do wywołania:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Poniższy kod pokazuje wartości kluczy dla zastąpionych kluczy:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Uruchom następujące polecenie, aby przetestować wymianę klucza:

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Uwaga: Obecnie `=` nie można używać do ustawiania wartości `-`zastępowania kluczy za pomocą pojedynczej kreski . Zobacz [ten problem z githubem](https://github.com/dotnet/extensions/issues/3059).

Następujące polecenie działa w celu przetestowania wymiany klucza:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

W przypadku aplikacji korzystających z `CreateDefaultBuilder` mapowań przełączników wywołanie nie powinno przekazywać argumentów. Wywołanie metody nie zawiera mapowanych przełączników i nie ma możliwości przekazania słownika mapowania przełączników do `CreateDefaultBuilder`. `CreateDefaultBuilder` `AddCommandLine` Rozwiązanie nie jest przekazywanie argumentów, `CreateDefaultBuilder` ale zamiast `ConfigurationBuilder` tego, `AddCommandLine` aby umożliwić metody do przetwarzania zarówno argumenty i słownik mapowania przełącznika.

## <a name="hierarchical-configuration-data"></a>Hierarchiczne dane konfiguracyjne

Interfejs API konfiguracji odczytuje dane konfiguracji hierarchicznej przez spłaszczenie danych hierarchicznych przy użyciu ogranicznika w kluczach konfiguracyjnych.

[Przykładowe pobieranie](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *appsettings.json:*

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka ustawień konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Preferowanym sposobem odczytywania danych konfiguracji hierarchicznej jest użycie wzorca opcji. Aby uzyskać więcej informacji, zobacz [Powiązanie danych konfiguracji hierarchicznej](#optpat) w tym dokumencie.

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>i <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody są dostępne do izolowania sekcji i obrażeń części sekcji w danych konfiguracyjnych. Metody te są opisane później w [GetSection, GetChildren i Exists](#getsection).

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Klucze i wartości konfiguracji

Klucze konfiguracyjne:

* Czy niewrażliwe na decyzje. Na przykład `ConnectionString` `connectionstring` i są traktowane jako klucze równoważne.
* Jeśli klucz i wartość jest ustawiona w więcej niż jeden dostawca konfiguracji, używana jest wartość z ostatniego dostawcy dodane. Aby uzyskać więcej informacji, zobacz [Konfiguracja domyślna](#default).
* Klucze hierarchiczne
  * W interfejsie API konfiguracji separator dwukropka (`:`) działa na wszystkich platformach.
  * W zmiennych środowiskowych separator dwukropek może nie działać na wszystkich platformach. Podwójne podkreślenie, `__`jest obsługiwane przez wszystkie platformy i jest `:`automatycznie konwertowane na dwukropek.
  * W usłudze Azure Key `--` Vault klucze hierarchiczne używają jako separatora. [Dostawca konfiguracji usługi Azure Key](xref:security/key-vault-configuration) `--` Vault `:` automatycznie zastępuje się, gdy wpisy tajne są ładowane do konfiguracji aplikacji.
* Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych. Powiązanie tablicy jest opisane w [Bind tablicy do](#boa) sekcji klasy.

Wartości konfiguracji:

* Są ciągami.
* Wartości null nie mogą być przechowywane w konfiguracji lub powiązane z obiektami.

<a name="cp"></a>

## <a name="configuration-providers"></a>Dostawcy konfiguracji

W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET aplikacji Core.

| Dostawca | Zapewnia konfigurację z |
| -------- | ----------------------------------- |
| [Dostawca konfiguracji usługi Azure Key Vault](xref:security/key-vault-configuration) | W usłudze Azure Key Vault |
| [Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration |
| [Dostawca konfiguracji wiersza polecenia](#clcp) | Parametry wiersza polecenia |
| [Niestandardowy dostawca konfiguracji](#custom-configuration-provider) | Źródło niestandardowe |
| [Dostawca konfiguracji zmiennych środowiskowych](#evcp) | Zmienne środowiskowe |
| [Dostawca konfiguracji plików](#file-configuration-provider) | Pliki INI, JSON i XML |
| [Dostawca konfiguracji klucza na plik](#key-per-file-configuration-provider) | Pliki katalogów |
| [Dostawca konfiguracji pamięci](#memory-configuration-provider) | Kolekcje w pamięci |
| [Tajny menedżer](xref:security/app-secrets)  | Plik w katalogu profilu użytkownika |

Źródła konfiguracji są odczytywane w kolejności, w yszczególnie ich dostawców konfiguracji. Dostawców konfiguracji zamówienia w kodzie, aby dopasować priorytety dla podstawowych źródeł konfiguracji, które wymaga aplikacji.

Typowa sekwencja dostawców konfiguracji to:

1. *appsettings.json*
1. *appsettings*. `Environment`. *json ( json )*
1. [Tajny menedżer](xref:security/app-secrets)
1. Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmienne środowiskowe](#evcp).
1. Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).

Powszechną praktyką jest dodanie dostawcy konfiguracji wiersza polecenia jako ostatniego w serii dostawców, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji ustawionej przez innych dostawców.

Poprzednia sekwencja dostawców jest używana w [konfiguracji domyślnej](#default).

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Prefiksy ciągu połączenia

Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych ciągu połączenia. Te parametry połączenia są zaangażowane w konfigurowanie ciągów połączeń platformy Azure dla środowiska aplikacji. Zmienne środowiskowe z prefiksami pokazanymi w tabeli są ładowane do `AddEnvironmentVariables`aplikacji z [domyślną konfiguracją](#default) lub gdy do pliku .

| Prefiks ciągu połączenia | Dostawca |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Dostawca niestandardowy |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Po wykryciu i załadowaniu zmiennej środowiskowej do konfiguracji z dowolnym z czterech prefiksów pokazanych w tabeli:

* Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej`ConnectionStrings`środowiskowej i dodanie sekcji klucza konfiguracji ( ).
* Tworzona jest nowa para klucza konfiguracji i wartość `CUSTOMCONNSTR_`reprezentująca dostawcę połączenia bazy danych (z wyjątkiem , który nie ma określonego dostawcy).

| Klucz zmiennej środowiskowej | Przekonwertowany klucz konfiguracji | Wpis konfiguracji dostawcy                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Wpis konfiguracji nie został utworzony.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`System.Data.SqlClient`  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>Dostawca konfiguracji JSON

Konfiguracja <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> ładowania z par klucza klucza json.

Przeciążenia można określić:

* Czy plik jest opcjonalny.
* Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.

Spójrzmy na poniższy kod:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Powyższy kod ma następujące działanie:

* Konfiguruje dostawcę konfiguracji JSON, aby załadować plik *MyConfig.json* z następującymi opcjami:
  * `optional: true`: Plik jest opcjonalny.
  * `reloadOnChange: true`: Plik zostanie ponownie załadowany po zapisaniu zmian.
* Odczytuje [domyślnych dostawców konfiguracji](#default) przed plikiem *MyConfig.json.* Ustawienia w ustawieniu zastępowania pliku *MyConfig.json* w domyślnych dostawcach konfiguracji, w tym [dostawcy konfiguracji zmiennych środowiska](#evcp) i dostawcy konfiguracji [wiersza polecenia](#clcp).

Zazwyczaj ***nie*** chcesz, aby niestandardowe wartości zastępowania pliku JSON były ustawiane w [dostawcy konfiguracji zmiennych środowiska](#evcp) i dostawcy konfiguracji [wiersza polecenia.](#clcp)

Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

W poprzednim kodzie ustawienia w *plikach MyConfig.json* i *MyConfig*. `Environment`. *pliki json:*

* Zastąpokaj ustawienia w *pliku appsettings.json* i *appsettings*. `Environment`. *json* plików.
* Są zastępowane przez ustawienia dostawcy [konfiguracji zmiennych środowiska](#evcp) i [dostawcy konfiguracji wiersza polecenia](#clcp).

[Przykładowy plik pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyConfig.json:*

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Dostawca konfiguracji plików

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą podstawową do ładowania konfiguracji z systemu plików. Następujący dostawcy konfiguracji wywodzą się z: `FileConfigurationProvider`

* [Dostawca konfiguracji INI](#ini-configuration-provider)
* [Dostawca konfiguracji JSON](#jcp)
* [Dostawca konfiguracji XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Dostawca konfiguracji INI

Konfiguracja <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> ładowania z par klucza klucza-wartość pliku INI w czasie wykonywania.

Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

W poprzednim kodzie ustawienia w *myiniconfig.ini* i *MyIniConfig*. `Environment`. pliki *ini* są zastępowane przez ustawienia w:

* [Dostawca konfiguracji zmiennych środowiskowych](#evcp)
* [Dostawca konfiguracji wiersza polecenia](#clcp).

[Przykładowy plik pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyIniConfig.ini:*

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>Dostawca konfiguracji XML

Konfiguracja <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> ładowania z par klucza klucza-wartość pliku XML w czasie wykonywania.

Poniższy kod czyści wszystkich dostawców konfiguracji i dodaje kilku dostawców konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

W poprzednim kodzie ustawienia w *plikach MyXMLFile.xml* i *MyXMLFile*. `Environment`. Pliki *xml* są zastępowane przez ustawienia w:

* [Dostawca konfiguracji zmiennych środowiskowych](#evcp)
* [Dostawca konfiguracji wiersza polecenia](#clcp).

[Przykładowy plik download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zawiera następujący plik *MyXMLFile.xml:*

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

Poniższy kod z [przykładowego pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla kilka poprzednich ustawień konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Powtarzające się elementy, które używają `name` tej samej nazwy elementu, działają, jeśli atrybut jest używany do rozróżniania elementów:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Poniższy kod odczytuje poprzedni plik konfiguracyjny i wyświetla klucze i wartości:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Atrybuty mogą być używane do dostarczania wartości:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`

* klucz:atrybut
* sekcja:klucz:atrybut

## <a name="key-per-file-configuration-provider"></a>Dostawca konfiguracji klucza na plik

Używa <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> plików katalogu jako par klucza konfiguracji wartości. Kluczem jest nazwa pliku. Wartość zawiera zawartość pliku. Dostawca konfiguracji klucza na plik jest używany w scenariuszach hostingu platformy Docker.

Aby aktywować konfigurację klucza <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> na plik, należy <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>wywołać metodę rozszerzenia w wystąpieniu programu . Do `directoryPath` plików musi być ścieżka bezwzględna.

Przeciążenia pozwalają na określenie:

* Pełnomocnik, `Action<KeyPerFileConfigurationSource>` który konfiguruje źródło.
* Czy katalog jest opcjonalny i ścieżka do katalogu.

Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików. Na przykład nazwa `Logging__LogLevel__System` pliku tworzy klucz `Logging:LogLevel:System`konfiguracji .

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Dostawca konfiguracji pamięci

Używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekcji w pamięci jako pary klucz konfiguracji wartość.

Poniższy kod dodaje kolekcję pamięci do systemu konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

Następujący kod z [pobierania przykładowego](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) wyświetla poprzednie ustawienia konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

W poprzednim kodzie `config.AddInMemoryCollection(Dict)` jest dodawany po [domyślnych dostawców konfiguracji](#default). Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).

Aby uzyskać przykład zamawiania dostawców konfiguracji, zobacz [dostawcę konfiguracji JSON](#jcp).

Zobacz [Powiąż tablicę](#boa) dla innego przykładu przy użyciu `MemoryConfigurationProvider`.

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji za pomocą określonego klucza i konwertuje ją na określony typ:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

W poprzednim kodzie, `NumberKey` jeśli nie zostanie znaleziony w konfiguracji, używana jest wartość domyślna. `99`

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren i istnieje

W kolejnych przykładach należy wziąć pod uwagę następujący plik *MySubsection.json:*

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Poniższy kod dodaje *MySubsection.json* do dostawców konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>Getsection

[Program IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) zwraca podsekcję konfiguracji z określonym kluczem podsekcji.

Następujący kod zwraca `section1`wartości dla:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Następujący kod zwraca `section2:subsection0`wartości dla:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection`nigdy `null`nie zwraca . Jeśli pasująca sekcja nie zostanie `IConfigurationSection` znaleziona, zwracana jest pusta sekcja.

Po `GetSection` zwróceniu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> pasującej sekcji nie jest wypełniona. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> i są zwracane, gdy sekcja istnieje.

### <a name="getchildren-and-exists"></a>GetChildren i istnieje

Poniższy kod wywołuje [iConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) `section2:subsection0`i zwraca wartości dla:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Poprzedni kod wywołuje [ConfigurationExtensions.Exists,](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) aby sprawdzić, czy istnieje sekcja:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Powiązyj tablicę

[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) obsługuje tablice powiązania do obiektów przy użyciu indeksów tablic w kluczach konfiguracyjnych. Każdy format tablicy, który udostępnia segment klucza numerycznego jest w stanie powiązania tablicy do tablicy klasy [POCO.](https://wikipedia.org/wiki/Plain_Old_CLR_Object)

Rozważmy *MyArray.json* z [próbki do pobrania:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Poniższy kod dodaje *MyArray.json* do dostawców konfiguracji:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Następujący kod odczytuje konfigurację i wyświetla wartości:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Poprzedni kod zwraca następujące dane wyjściowe:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

W poprzednim wyjściu indeks 3 `value40`ma `"4": "value40",` wartość odpowiadającą *w myarray.json*. Indeksy tablicy powiązanej są ciągłe i nie są powiązane z indeksem klucza konfiguracji. Spinacza konfiguracji nie może wiązać wartości null lub tworzyć wpisy null w obiektach powiązanych

Następujący kod ładuje `array:entries` konfigurację <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> za pomocą metody rozszerzenia:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Poniższy kod odczytuje `arrayDict` `Dictionary` konfigurację w i wyświetla wartości:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Poprzedni kod zwraca następujące dane wyjściowe:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Indeks &num;3 w obiekcie powiązanym `array:4` przechowuje dane konfiguracyjne klucza konfiguracji i jego wartość `value4`. Gdy dane konfiguracyjne zawierające tablicę są powiązane, indeksy tablicowe w kluczach konfiguracyjnych są używane do iteracji danych konfiguracji podczas tworzenia obiektu. Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracyjnych pomija jeden lub więcej indeksów.

Brakujący element konfiguracji &num;dla indeksu 3 `ArrayExample` może być dostarczony przed powiązaniem z wystąpieniem przez dowolnego dostawcę konfiguracji, który odczytuje parę klucz/wartość indeksu &num;3. Należy wziąć pod uwagę następujący plik *Value3.json* z przykładowego pobrania:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Poniższy kod zawiera konfigurację *dla value3.json* `arrayDict` `Dictionary`i:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Poniższy kod odczytuje poprzednią konfigurację i wyświetla wartości:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Poprzedni kod zwraca następujące dane wyjściowe:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Dostawcy konfiguracji niestandardowej nie są wymagane do zaimplementowania powiązania tablicy.

## <a name="custom-configuration-provider"></a>Niestandardowy dostawca konfiguracji

Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucza konfiguracji z bazy danych przy użyciu [entity framework (EF)](/ef/core/).

Dostawca posiada następujące cechy:

* Baza danych EF w pamięci jest używana do celów demonstracyjnych. Aby użyć bazy danych, która wymaga `ConfigurationBuilder` ciągu połączenia, należy zaimplementować pomocniczy do poniesienia ciągu połączenia od innego dostawcy konfiguracji.
* Dostawca odczytuje tabelę bazy danych do konfiguracji podczas uruchamiania. Dostawca nie wysyła zapytania do bazy danych na podstawie klucza.
* Ponowne ładowanie na zmianę nie jest zaimplementowana, więc aktualizowanie bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.

Zdefiniuj encję `EFConfigurationValue` do przechowywania wartości konfiguracji w bazie danych.

*Modele/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Dodaj `EFConfigurationContext` do przechowywania i dostępu do skonfigurowanych wartości.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Utwórz klasę implementuują . <xref:Microsoft.Extensions.Configuration.IConfigurationSource>

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Utwórz niestandardowego dostawcę <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>konfiguracji, dziedzicząc po programie . Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta. Ponieważ [klucze konfiguracyjne są niewrażliwe na rozmiary,](#keys)słownik używany do inicjowania bazy danych jest tworzony za pomocą modułu porównującego bez uwzględniania wielkości liter ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda `AddEFConfiguration` rozszerzenia umożliwia dodanie źródła `ConfigurationBuilder`konfiguracji do pliku .

*Rozszerzenia/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Poniższy kod pokazuje, jak `EFConfigurationProvider` używać niestandardowych w *Program.cs:*

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Konfiguracja dostępu podczas uruchamiania

Następujący kod wyświetla dane `Startup` konfiguracyjne w metodach:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Na przykład uzyskiwania dostępu do konfiguracji przy użyciu metod wygody uruchamiania, zobacz [Uruchamianie aplikacji: Metody wygody](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-razor-pages"></a>Konfiguracja dostępu na stronach Razor

Następujący kod wyświetla dane konfiguracyjne na stronie Razor:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Konfiguracja dostępu w pliku widoku MVC

Następujący kod wyświetla dane konfiguracyjne w widoku MVC:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Konfiguracja hosta i aplikacji

Przed skonfigurowaniem i uruchomieniem aplikacji *host* jest konfigurowany i uruchamiany. Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia. Zarówno aplikacja, jak i host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie. Pary klucza-wartość konfiguracji hosta są również uwzględniane w konfiguracji aplikacji. Aby uzyskać więcej informacji na temat sposobu, w jaki dostawcy konfiguracji są <xref:fundamentals/index#host>używane podczas budowy hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz .

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Domyślna konfiguracja hosta

Szczegółowe informacje na temat konfiguracji domyślnej podczas korzystania z [hosta sieci Web](xref:fundamentals/host/web-host)można znaleźć w [ASP.NET wersji Core 2.2 w tym temacie](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Konfiguracja hosta jest dostarczana z:
  * Zmienne środowiskowe poprzedzone `DOTNET_` (na `DOTNET_ENVIRONMENT`przykład) przy użyciu dostawcy konfiguracji [Zmienne środowiskowe](#environment-variables-configuration-provider). Prefiks`DOTNET_`( ) jest usuwany po załadowaniu par klucza konfiguracji-wartość.
  * Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).
* Domyślna konfiguracja hosta`ConfigureWebHostDefaults`sieci Web jest ustanawiana ( ):
  * Pustułka jest używana jako serwer www i konfigurowana przy użyciu dostawców konfiguracji aplikacji.
  * Dodaj oprogramowanie pośredniczące filtrowania hostów.
  * Dodaj oprogramowanie pośredniczące nagłówków przesyłanych dalej, jeśli zmienna środowiskowa `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest ustawiona na `true`.
  * Włącz integrację z usługiami IIS.

## <a name="other-configuration"></a>Inna konfiguracja

Ten temat dotyczy tylko *konfiguracji aplikacji*. Inne aspekty uruchamiania i hostingu aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracyjnych, które nie zostały omówione w tym temacie:

* *launch.json*/*launchSettings.json* są oprzyrządowanie plików konfiguracyjnych dla środowiska programistycznego, opisane:
  * W <xref:fundamentals/environments#development>.
  * W całej dokumentacji zestaw, gdzie pliki są używane do konfigurowania ASP.NET podstawowe aplikacje dla scenariuszy rozwoju.
* *web.config* to plik konfiguracyjny serwera, opisany w następujących tematach:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Aby uzyskać więcej informacji na temat migracji konfiguracji aplikacji <xref:migration/proper-to-2x/index#store-configurations>z wcześniejszych wersji ASP.NET, zobacz .

## <a name="add-configuration-from-an-external-assembly"></a>Dodawanie konfiguracji z zespołu zewnętrznego

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Kod źródłowy konfiguracji](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Konfiguracja aplikacji w ASP.NET Core opiera się na parach klucz-wartość ustanowiona przez *dostawców konfiguracji.* Dostawcy konfiguracji odczytywać dane konfiguracyjne do par klucz-wartość z różnych źródeł konfiguracji:

* W usłudze Azure Key Vault
* Azure App Configuration
* Argumenty wiersza polecenia
* Dostawcy niestandardowi (zainstalowane lub utworzone)
* Pliki katalogów
* Zmienne środowiskowe
* Obiekty NET w pamięci
* Pliki ustawień

Pakiety konfiguracyjne dla typowych scenariuszy dostawców konfiguracji ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) są zawarte w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Przykłady kodu, które należy wykonać <xref:Microsoft.Extensions.Configuration> i w przykładowej aplikacji używać obszaru nazw:

```csharp
using Microsoft.Extensions.Configuration;
```

*Wzorzec opcji* jest rozszerzeniem koncepcji konfiguracji opisanych w tym temacie. Opcje używają klas do reprezentowania grup powiązanych ustawień. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Konfiguracja hosta i aplikacji

Przed skonfigurowaniem i uruchomieniem aplikacji *host* jest konfigurowany i uruchamiany. Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia. Zarówno aplikacja, jak i host są konfigurowane przy użyciu dostawców konfiguracji opisanych w tym temacie. Pary klucza-wartość konfiguracji hosta są również uwzględniane w konfiguracji aplikacji. Aby uzyskać więcej informacji na temat sposobu, w jaki dostawcy konfiguracji są <xref:fundamentals/index#host>używane podczas budowy hosta i jak źródła konfiguracji wpływają na konfigurację hosta, zobacz .

## <a name="other-configuration"></a>Inna konfiguracja

Ten temat dotyczy tylko *konfiguracji aplikacji*. Inne aspekty uruchamiania i hostingu aplikacji ASP.NET Core są konfigurowane przy użyciu plików konfiguracyjnych, które nie zostały omówione w tym temacie:

* *launch.json*/*launchSettings.json* są oprzyrządowanie plików konfiguracyjnych dla środowiska programistycznego, opisane:
  * W <xref:fundamentals/environments#development>.
  * W całej dokumentacji zestaw, gdzie pliki są używane do konfigurowania ASP.NET podstawowe aplikacje dla scenariuszy rozwoju.
* *web.config* to plik konfiguracyjny serwera, opisany w następujących tematach:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Aby uzyskać więcej informacji na temat migracji konfiguracji aplikacji <xref:migration/proper-to-2x/index#store-configurations>z wcześniejszych wersji ASP.NET, zobacz .

## <a name="default-configuration"></a>Konfiguracja domyślna

Aplikacje sieci Web oparte na ASP.NET Core [dotnet nowe](/dotnet/core/tools/dotnet-new) szablony wywołać <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> podczas tworzenia hosta. `CreateDefaultBuilder`zapewnia domyślną konfigurację aplikacji w następującej kolejności:

Poniżej przedstawiono aplikacje korzystające z [hosta sieci Web](xref:fundamentals/host/web-host). Aby uzyskać szczegółowe informacje na temat domyślnej konfiguracji podczas korzystania z [hosta ogólnego,](xref:fundamentals/host/generic-host)zobacz [najnowszą wersję tego tematu](xref:fundamentals/configuration/index).

* Konfiguracja hosta jest dostarczana z:
  * Zmienne środowiskowe poprzedzone `ASPNETCORE_` (na `ASPNETCORE_ENVIRONMENT`przykład) przy użyciu dostawcy konfiguracji [zmiennych środowiskowych](#environment-variables-configuration-provider). Prefiks`ASPNETCORE_`( ) jest usuwany po załadowaniu par klucza konfiguracji-wartość.
  * Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).
* Konfiguracja aplikacji jest dostarczana z:
  * *appsettings.json* za pomocą [dostawcy konfiguracji plików](#file-configuration-provider).
  * *appsettings. {Środowisko}.json* przy użyciu [dostawcy konfiguracji plików](#file-configuration-provider).
  * [Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku przy użyciu zestawu wejścia.
  * Zmienne środowiskowe przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider).
  * Argumenty wiersza polecenia przy użyciu [dostawcy konfiguracji wiersza polecenia](#command-line-configuration-provider).

## <a name="security"></a>Zabezpieczenia

Przyjęcie następujących rozwiązań w celu zabezpieczenia poufnych danych konfiguracyjnych:

* Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie dostawcy konfiguracji lub w plikach konfiguracyjnych w postaci zwykłego tekstu.
* Nie używaj wpisów tajnych produkcji w środowiskach deweloperskich lub testowych.
* Określ wpisy tajne poza projektem, tak aby nie można przypadkowo zatwierdzone do repozytorium kodu źródłowego.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>&ndash; Zawiera porady dotyczące używania zmiennych środowiskowych do przechowywania poufnych danych. Menedżer tajny używa dostawcy konfiguracji plików do przechowywania wpisów tajnych użytkownika w pliku JSON w systemie lokalnym. Dostawca konfiguracji plików jest opisany w dalszej części tego tematu.

[Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpiecznie przechowuje wpisy tajne aplikacji dla aplikacji ASP.NET Core. Aby uzyskać więcej informacji, zobacz <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hierarchiczne dane konfiguracyjne

Interfejs API konfiguracji jest w stanie obsługi danych konfiguracji hierarchicznej przez spłaszczenie danych hierarchicznych za pomocą ogranicznika w kluczach konfiguracyjnych.

W następującym pliku JSON istnieją cztery klucze w ustrukturyzowanej hierarchii dwóch sekcji:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Gdy plik jest odczytywany do konfiguracji, unikatowe klucze są tworzone w celu utrzymania oryginalnej hierarchicznej struktury danych źródła konfiguracji. Sekcje i klawisze są spłaszczane za`:`pomocą dwukropka ( ) w celu utrzymania oryginalnej struktury:

* section0:key0
* section0:key1
* sekcja1:key0
* sekcja1:key1

<xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>i <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> metody są dostępne do izolowania sekcji i obrażeń części sekcji w danych konfiguracyjnych. Metody te są opisane później w [GetSection, GetChildren i Exists](#getsection-getchildren-and-exists).

## <a name="conventions"></a>Konwencja

### <a name="sources-and-providers"></a>Źródła i dostawcy

Podczas uruchamiania aplikacji źródła konfiguracji są odczytywane w kolejności, w yszczególnie ich dostawców konfiguracji.

Dostawcy konfiguracji, którzy implementują wykrywanie zmian, mają możliwość ponownego ładowania konfiguracji po zmianie ustawienia źródłowego. Na przykład dostawca konfiguracji plików (opisany w dalszej części tego tematu) i [dostawca konfiguracji usługi Azure Key Vault](xref:security/key-vault-configuration) implementują wykrywanie zmian.

<xref:Microsoft.Extensions.Configuration.IConfiguration>jest dostępna w kontenerze [iniekcji zależności aplikacji (DI).](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Configuration.IConfiguration>można wstrzyknąć do <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> strony <xref:Microsoft.AspNetCore.Mvc.Controller> Razor lub MVC w celu uzyskania konfiguracji dla klasy.

W poniższych przykładach `_config` pole jest używane do uzyskiwania dostępu do wartości konfiguracji:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Dostawcy konfiguracji nie mogą korzystać z DI, ponieważ nie jest on dostępny, gdy są konfiguracyjnie przez hosta.

### <a name="keys"></a>Klucze

Klucze konfiguracji przyjmują następujące konwencje:

* Klucze są niewrażliwe na wielkości liter. Na przykład `ConnectionString` `connectionstring` i są traktowane jako klucze równoważne.
* Jeśli wartość dla tego samego klucza jest ustawiana przez tych samych lub różnych dostawców konfiguracji, ostatnią wartością ustawioną na klucz jest używana wartość.
* Klucze hierarchiczne
  * W interfejsie API konfiguracji separator dwukropka (`:`) działa na wszystkich platformach.
  * W zmiennych środowiskowych separator dwukropek może nie działać na wszystkich platformach. Podwójne podkreślenie (`__`) jest obsługiwane przez wszystkie platformy i jest automatycznie konwertowane na dwukropek.
  * W usłudze Azure Key `--` Vault klucze hierarchiczne używają (dwóch kresek) jako separatora. Napisz kod, aby zastąpić kreski dwukropek, gdy wpisy tajne są ładowane do konfiguracji aplikacji.
* Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych. Powiązanie tablicy jest opisane w [Bind tablicy do](#bind-an-array-to-a-class) sekcji klasy.

### <a name="values"></a>Wartości

Wartości konfiguracji przyjmują następujące konwencje:

* Wartości są ciągami znaków.
* Wartości null nie mogą być przechowywane w konfiguracji lub powiązane z obiektami.

## <a name="providers"></a>Dostawcy

W poniższej tabeli przedstawiono dostawców konfiguracji dostępnych do ASP.NET aplikacji Core.

| Dostawca | Zapewnia konfigurację z&hellip; |
| -------- | ----------------------------------- |
| [Dostawca konfiguracji usługi Azure Key Vault](xref:security/key-vault-configuration) (zagadnienia dotyczące*zabezpieczeń)* | W usłudze Azure Key Vault |
| [Dostawca konfiguracji aplikacji platformy Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentacja platformy Azure) | Azure App Configuration |
| [Dostawca konfiguracji wiersza polecenia](#command-line-configuration-provider) | Parametry wiersza polecenia |
| [Niestandardowy dostawca konfiguracji](#custom-configuration-provider) | Źródło niestandardowe |
| [Dostawca konfiguracji zmiennych środowiskowych](#environment-variables-configuration-provider) | Zmienne środowiskowe |
| [Dostawca konfiguracji plików](#file-configuration-provider) | Pliki (INI, JSON, XML) |
| [Dostawca konfiguracji klucza na plik](#key-per-file-configuration-provider) | Pliki katalogów |
| [Dostawca konfiguracji pamięci](#memory-configuration-provider) | Kolekcje w pamięci |
| [Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) ( Tematy*zabezpieczeń)* | Plik w katalogu profilu użytkownika |

Źródła konfiguracji są odczytywane w kolejności, w ich dostawców konfiguracji są określone podczas uruchamiania. Dostawcy konfiguracji opisane w tym temacie są opisane w kolejności alfabetycznej, a nie w kolejności, w porządku, że kod organizuje je. Dostawców konfiguracji zamówienia w kodzie, aby dopasować priorytety dla podstawowych źródeł konfiguracji, które wymaga aplikacji.

Typowa sekwencja dostawców konfiguracji to:

1. Pliki (*appsettings.json*, *appsettings.{ Środowisko}.json*, `{Environment}` gdzie jest bieżące środowisko hostingowe aplikacji)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Wpisy tajne użytkownika (secret manager)](xref:security/app-secrets) (tylko środowisko programistyczne)
1. Zmienne środowiskowe
1. Argumenty wiersza polecenia

Powszechną praktyką jest umieszczenie dostawcy konfiguracji wiersza polecenia jako ostatniego w serii dostawców, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji ustawionej przez innych dostawców.

Poprzednia sekwencja dostawców jest używana, gdy nowy konstruktor hosta jest inicjowany za pomocą pliku `CreateDefaultBuilder`. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurowanie konstruktora hosta za pomocą funkcji UseConfiguration

Aby skonfigurować konstruktora <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> hosta, wywołaj konstruktora hosta z konfiguracją.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>Konfigurowanie konfiguracjiappconfiguration

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić dostawców konfiguracji aplikacji `CreateDefaultBuilder`oprócz tych dodawanych automatycznie przez:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Zastępowanie poprzedniej konfiguracji argumentami wiersza polecenia

Aby zapewnić konfigurację aplikacji, którą można zastąpić argumentami wiersza polecenia, zadzwoń `AddCommandLine` jako ostatni:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Usuń dostawców dodanych przez CreateDefaultBuilder

Aby usunąć dostawców `CreateDefaultBuilder`dodanych przez , najpierw wywołaj [clear](/dotnet/api/system.collections.generic.icollection-1.clear) na [iConfigurationBuilder.Sources:](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Korzystanie z konfiguracji podczas uruchamiania aplikacji

Konfiguracja dostarczona `ConfigureAppConfiguration` do aplikacji jest dostępna podczas `Startup.ConfigureServices`uruchamiania aplikacji, w tym . Aby uzyskać więcej informacji, zobacz [sekcję Konfiguracja programu Access podczas uruchamiania.](#access-configuration-during-startup)

## <a name="command-line-configuration-provider"></a>Dostawca konfiguracji wiersza polecenia

Konfiguracja <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> ładuje z par klucz-wartość argumentu wiersza polecenia w czasie wykonywania.

Aby aktywować konfigurację <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> wiersza polecenia, metoda <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>rozszerzenia jest wywoływana w wystąpieniu programu .

`AddCommandLine`jest wywoływana `CreateDefaultBuilder(string [])` automatycznie, gdy jest wywoływana. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)

`CreateDefaultBuilder`ładuje również:

* Opcjonalna konfiguracja z *pliku appsettings.json* i *appsettings.{ Środowiska}.json.*
* [Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.
* Zmienne środowiskowe.

`CreateDefaultBuilder`dodaje jako ostatniego dostawcę konfiguracji wiersza polecenia. Argumenty wiersza polecenia przekazywane w czasie wykonywania zastępują konfigurację ustawioną przez innych dostawców.

`CreateDefaultBuilder`działa, gdy host jest konstruowany. W związku z tym konfiguracja `CreateDefaultBuilder` wiersza polecenia aktywowana przez może mieć wpływ na sposób konfigurowania hosta.

W przypadku aplikacji opartych na szablonach `AddCommandLine` ASP.NET Core `CreateDefaultBuilder`został już wywołany przez program . Aby dodać dodatkowych dostawców konfiguracji i zachować możliwość zastąpienia konfiguracji od tych dostawców z argumentami `ConfigureAppConfiguration` wiersza polecenia, zadzwoń do dodatkowych dostawców aplikacji i wywołaj `AddCommandLine` ostatni.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Przykład**

Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>hosta, która zawiera wywołanie .

1. Otwórz wiersz polecenia w katalogu projektu.
1. Podaj argument wiersza `dotnet run` polecenia `dotnet run CommandLineKey=CommandLineValue`do polecenia , .
1. Po uruchomieniu aplikacji otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .
1. Należy zauważyć, że dane wyjściowe zawierają parę klucz-wartość `dotnet run`dla argumentu wiersza polecenia konfiguracji dostarczonego do .

### <a name="arguments"></a>Argumenty

Wartość musi być zgodna ze`=`znakiem równości ( ) lub`--` `/`klucz musi mieć prefiks ( lub ), gdy wartość podąża za spacją. Wartość nie jest wymagana, jeśli używany jest znak równy (na przykład `CommandLineKey=`).

| Prefiks klucza               | Przykład                                                |
| ------------------------ | ------------------------------------------------------ |
| Brak prefiksu                | `CommandLineKey1=value1`                               |
| Dwie kreski`--`( )        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Ukośnik do przodu (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

W ramach tego samego polecenia nie należy mieszać par klucz-wartość argumentu wiersza polecenia, które używają znaku równości z parami klucz-wartość, które używają spacji.

Przykładowe polecenia:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Mapowania przełączników

Mapowania przełączników umożliwiają logikę zastępowania nazw kluczy. Podczas ręcznego budowania <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>konfiguracji za pomocą programu , <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> należy podać słownik zamienników przełączników do metody.

Gdy używany jest słownik mapowania przełącznika, słownik jest sprawdzany pod kątem klucza zgodnego z kluczem dostarczonym przez argument wiersza polecenia. Jeśli klucz wiersza polecenia zostanie znaleziony w słowniku, wartość słownika (zastąpienie klucza) jest przekazywana z powrotem, aby ustawić parę klucz-wartość w konfiguracji aplikacji. Mapowanie przełącznika jest wymagane dla każdego klucza wiersza`-`polecenia poprzedzonym pojedynczą kreską ( ).

Przełącz reguły klucza słownika mapowania:

* Przełączniki muszą zaczynać`-`się od kreski`--`( ) lub podwójnej kreski ( ).
* Słownik mapowań przełączników nie może zawierać zduplikowanych kluczy.

Tworzenie słownika mapowań przełączników. W poniższym przykładzie tworzone są dwa mapowania przełączników:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Po zbudowaniu hosta wywołaj ze `AddCommandLine` słownikiem mapowań przełączników:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

W przypadku aplikacji korzystających z `CreateDefaultBuilder` mapowań przełączników wywołanie nie powinno przekazywać argumentów. Wywołanie metody nie zawiera mapowanych przełączników i nie ma możliwości przekazania słownika `CreateDefaultBuilder`mapowania przełącznika do . `CreateDefaultBuilder` `AddCommandLine` Rozwiązanie nie jest przekazywanie argumentów, `CreateDefaultBuilder` ale zamiast `ConfigurationBuilder` tego, `AddCommandLine` aby umożliwić metody do przetwarzania zarówno argumenty i słownik mapowania przełącznika.

Po utworzeniu słownika mapowania przełączników zawiera on dane przedstawione w poniższej tabeli.

| Klucz       | Wartość             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Jeśli podczas uruchamiania aplikacji są używane klucze mapowane przez przełączniki, konfiguracja odbiera wartość konfiguracji klucza dostarczonego przez słownik:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Po uruchomieniu poprzedniego polecenia konfiguracja zawiera wartości pokazane w poniższej tabeli.

| Klucz               | Wartość    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Dostawca konfiguracji zmiennych środowiskowych

Konfiguracja <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> obciążeń z par klucza klucza-wartości zmiennej środowiskowej w czasie wykonywania.

Aby aktywować konfigurację zmiennych środowiskowych, należy wywołać metodę <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Usługa Azure App Service](https://azure.microsoft.com/services/app-service/) zezwala na ustawianie zmiennych środowiskowych w portalu Azure Portal, które mogą zastępować konfigurację aplikacji przy użyciu dostawcy konfiguracji zmiennych środowiskowych. Aby uzyskać więcej informacji, zobacz [Usługi Azure Apps: Zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables`służy do ładowania zmiennych środowiskowych `ASPNETCORE_` poprzedzonych konfiguracją [hosta,](#host-versus-app-configuration) gdy nowy konstruktor hosta jest inicjowany za pomocą [hosta sieci Web](xref:fundamentals/host/web-host) i `CreateDefaultBuilder` jest wywoływany. Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)

`CreateDefaultBuilder`ładuje również:

* Konfiguracja aplikacji z nieprefikowanych `AddEnvironmentVariables` zmiennych środowiskowych przez wywołanie bez prefiksu.
* Opcjonalna konfiguracja z *pliku appsettings.json* i *appsettings.{ Środowiska}.json.*
* [Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.
* Argumenty wiersza polecenia.

Dostawca konfiguracji zmiennych środowiskowych jest wywoływany po ustanowieniu konfiguracji z plików *wpisów tajnych i appsettings* użytkownika. Wywołanie dostawcy w tej pozycji umożliwia zmienne środowiskowe odczytywane w czasie wykonywania, aby zastąpić konfigurację ustawioną przez wpisy tajne użytkownika i pliki *appsettings.*

Aby zapewnić konfigurację aplikacji z dodatkowych zmiennych środowiskowych, `ConfigureAppConfiguration` zadzwoń `AddEnvironmentVariables` do dodatkowych dostawców aplikacji i zadzwoń z prefiksem:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Wywołanie `AddEnvironmentVariables` ostatni, aby umożliwić zmienne środowiskowe z danym prefiksem, aby zastąpić wartości od innych dostawców.

**Przykład**

Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia `AddEnvironmentVariables`hosta, która zawiera wywołanie .

1. Uruchom przykładową aplikację. Otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .
1. Należy zauważyć, że dane wyjściowe zawierają `ENVIRONMENT`parę klucz-wartość dla zmiennej środowiskowej . Wartość odzwierciedla środowisko, w którym aplikacja jest `Development` uruchomiona, zazwyczaj podczas uruchamiania lokalnie.

Aby zachować listę zmiennych środowiskowych renderowane przez aplikację krótki, aplikacja filtruje zmienne środowiskowe. Zobacz przykładowy plik *Pages/Index.cshtml.cs* aplikacji.

Aby udostępnić wszystkie zmienne środowiskowe dostępne `FilteredConfiguration` dla aplikacji, zmień w *pages/index.cshtml.cs* na następujące:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Prefiksy

Zmienne środowiskowe ładowane do konfiguracji aplikacji są filtrowane `AddEnvironmentVariables` podczas dostarczania prefiksu do metody. Na przykład, aby filtrować zmienne `CUSTOM_`środowiskowe w prefiksie, podaj prefiks dostawcy konfiguracji:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Prefiks jest usuwany podczas tworzenia par klucza konfiguracji-wartość.

Po utworzeniu konstruktora hosta konfiguracja hosta jest dostarczana przez zmienne środowiskowe. Aby uzyskać więcej informacji na temat prefiksu używanego dla tych zmiennych środowiskowych, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)

**Prefiksy ciągu połączenia**

Interfejs API konfiguracji ma specjalne reguły przetwarzania dla czterech zmiennych środowiskowych ciągu połączenia zaangażowanych w konfigurowanie ciągów połączeń platformy Azure dla środowiska aplikacji. Zmienne środowiskowe z prefiksami pokazanymi w tabeli są ładowane do aplikacji, jeśli do `AddEnvironmentVariables`pliku .

| Prefiks ciągu połączenia | Dostawca |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Dostawca niestandardowy |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Po wykryciu i załadowaniu zmiennej środowiskowej do konfiguracji z dowolnym z czterech prefiksów pokazanych w tabeli:

* Klucz konfiguracji jest tworzony przez usunięcie prefiksu zmiennej`ConnectionStrings`środowiskowej i dodanie sekcji klucza konfiguracji ( ).
* Tworzona jest nowa para klucza konfiguracji i wartość `CUSTOMCONNSTR_`reprezentująca dostawcę połączenia bazy danych (z wyjątkiem , który nie ma określonego dostawcy).

| Klucz zmiennej środowiskowej | Przekonwertowany klucz konfiguracji | Wpis konfiguracji dostawcy                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Wpis konfiguracji nie został utworzony.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Klucz: `ConnectionStrings:{KEY}_ProviderName`:<br>Wartość:`System.Data.SqlClient`  |

**Przykład**

Na serwerze tworzona jest niestandardowa zmienna środowiskowa ciągu połączenia:

* Imię &ndash; i nazwisko`CUSTOMCONNSTR_ReleaseDB`
* Wartość &ndash;`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Jeśli `IConfiguration` jest wstrzykiwany i przypisany `_config`do pola o nazwie, przeczytaj wartość:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Dostawca konfiguracji plików

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>jest klasą podstawową do ładowania konfiguracji z systemu plików. Następujący dostawcy konfiguracji są dedykowane do określonych typów plików:

* [Dostawca konfiguracji INI](#ini-configuration-provider)
* [Dostawca konfiguracji JSON](#json-configuration-provider)
* [Dostawca konfiguracji XML](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>Dostawca konfiguracji INI

Konfiguracja <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> ładowania z par klucza klucza-wartość pliku INI w czasie wykonywania.

Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> INI, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Dwukropek może służyć jako ogranicznik sekcji w konfiguracji pliku INI.

Przeciążenia pozwalają na określenie:

* Czy plik jest opcjonalny.
* Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.
* Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Ogólny przykład pliku konfiguracyjnego INI:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`

* section0:key0
* section0:key1
* section1:podsekcja:klucz
* section2:podsekcja0:klucz
* section2:podsekcja1:klucz

### <a name="json-configuration-provider"></a>Dostawca konfiguracji JSON

Konfiguracja <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> ładowania z par klucza klucza json w czasie wykonywania.

Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> JSON, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Przeciążenia pozwalają na określenie:

* Czy plik jest opcjonalny.
* Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.
* Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.

`AddJsonFile`jest automatycznie wywoływana dwa razy, gdy nowy `CreateDefaultBuilder`konstruktor hosta jest inicjowany za pomocą pliku . Metoda jest wywoływana do ładowania konfiguracji z:

* *appsettings.json* &ndash; Ten plik jest odczytywany jako pierwszy. Wersja środowiska pliku może zastąpić wartości podane przez plik *appsettings.json.*
* *appsettings. {Środowisko}.json* &ndash; Wersja środowiska pliku jest ładowana na podstawie [pliku IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).

Aby uzyskać więcej informacji, zobacz sekcję [Konfiguracja domyślna.](#default-configuration)

`CreateDefaultBuilder`ładuje również:

* Zmienne środowiskowe.
* [Wpisy tajne użytkownika (Tajny menedżer)](xref:security/app-secrets) w środowisku deweloperskim.
* Argumenty wiersza polecenia.

Dostawca konfiguracji JSON jest ustanawiany jako pierwszy. W związku z tym wpisy tajne użytkownika, zmienne środowiskowe i argumenty wiersza polecenia zastępują konfigurację ustawioną przez pliki *zestawów aplikacji.*

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta w celu określenia konfiguracji aplikacji dla plików innych niż *appsettings.json* i *appsettings.{ Środowisko}.json*:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Przykład**

Przykładowa aplikacja korzysta z statycznej `CreateDefaultBuilder` metody wygody do tworzenia `AddJsonFile`hosta, która zawiera dwa wywołania:

* Pierwsze wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.json*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Drugie wywołanie `AddJsonFile` ładowania konfiguracji z *appsettings.{ Środowisko}.json*. Dla *appsettings. Development.json* w przykładowej aplikacji, załadowany jest następujący plik:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Uruchom przykładową aplikację. Otwórz przeglądarkę w `http://localhost:5000`aplikacji pod adresem .
1. Dane wyjściowe zawiera pary klucz-wartość dla konfiguracji na podstawie środowiska aplikacji. Poziom dziennika dla `Logging:LogLevel:Default` klucza jest `Debug` podczas uruchamiania aplikacji w środowisku deweloperskim.
1. Uruchom przykładową aplikację ponownie w środowisku produkcyjnym:
   1. Otwórz plik *Properties/launchSettings.json.*
   1. W `ConfigurationSample` profilu zmień wartość zmiennej środowiskowej na `ASPNETCORE_ENVIRONMENT` `Production`.
   1. Zapisz plik i uruchom `dotnet run` aplikację w powłoce poleceń.
1. Ustawienia w *appsettings. Development.json* nie zastępuje już ustawień w *pliku appsettings.json*. Poziom dziennika dla `Logging:LogLevel:Default` klucza to `Warning`.

### <a name="xml-configuration-provider"></a>Dostawca konfiguracji XML

Konfiguracja <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> ładowania z par klucza klucza-wartość pliku XML w czasie wykonywania.

Aby aktywować konfigurację pliku <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> XML, wywołaj metodę rozszerzenia w wystąpieniu programu <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.

Przeciążenia pozwalają na określenie:

* Czy plik jest opcjonalny.
* Czy konfiguracja jest ponownie ładowana, jeśli plik się zmieni.
* Używany <xref:Microsoft.Extensions.FileProviders.IFileProvider> do uzyskiwania dostępu do pliku.

Węzeł główny pliku konfiguracyjnego jest ignorowany podczas tworzenia par klucza konfiguracji i wartości. Nie określaj definicji typu dokumentu (DTD) ani przestrzeni nazw w pliku.

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

Pliki konfiguracyjne XML mogą używać różnych nazw elementów dla sekcji powtarzanych:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`

* section0:key0
* section0:key1
* sekcja1:key0
* sekcja1:key1

Powtarzające się elementy, które używają `name` tej samej nazwy elementu, działają, jeśli atrybut jest używany do rozróżniania elementów:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`

* sekcja:section0:key:key0
* sekcja:section0:key:key1
* sekcja:section1:key:key0
* sekcja:section1:key:key1

Atrybuty mogą być używane do dostarczania wartości:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Poprzedni plik konfiguracyjny ładuje następujące klucze za pomocą: `value`

* klucz:atrybut
* sekcja:klucz:atrybut

## <a name="key-per-file-configuration-provider"></a>Dostawca konfiguracji klucza na plik

Używa <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> plików katalogu jako par klucza konfiguracji wartości. Kluczem jest nazwa pliku. Wartość zawiera zawartość pliku. Dostawca konfiguracji klucza na plik jest używany w scenariuszach hostingu platformy Docker.

Aby aktywować konfigurację klucza <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> na plik, należy <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>wywołać metodę rozszerzenia w wystąpieniu programu . Do `directoryPath` plików musi być ścieżka bezwzględna.

Przeciążenia pozwalają na określenie:

* Pełnomocnik, `Action<KeyPerFileConfigurationSource>` który konfiguruje źródło.
* Czy katalog jest opcjonalny i ścieżka do katalogu.

Podwójny znak podkreślenia (`__`) jest używany jako ogranicznik klucza konfiguracji w nazwach plików. Na przykład nazwa `Logging__LogLevel__System` pliku tworzy klucz `Logging:LogLevel:System`konfiguracji .

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Dostawca konfiguracji pamięci

Używa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> kolekcji w pamięci jako pary klucz konfiguracji wartość.

Aby aktywować konfigurację kolekcji <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> w pamięci, <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>należy wywołać metodę rozszerzenia w wystąpieniu programu .

Dostawcę konfiguracji można zainicjować `IEnumerable<KeyValuePair<String,String>>`za pomocą pliku .

Wywołanie `ConfigureAppConfiguration` podczas tworzenia hosta, aby określić konfigurację aplikacji.

W poniższym przykładzie tworzony jest słownik konfiguracji:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Słownik jest używany z wywołaniem, aby `AddInMemoryCollection` zapewnić konfigurację:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)wyodrębnia pojedynczą wartość z konfiguracji za pomocą określonego klucza i konwertuje ją na określony typ niezwiązanych z składem. Przeciążenie akceptuje wartość domyślną.

Poniższy przykład:

* Wyodrębnia wartość ciągu z konfiguracji `NumberKey`za pomocą klucza . Jeśli `NumberKey` nie zostanie znaleziony w kluczach konfiguracyjnych, używana jest wartość domyślna. `99`
* Wpisuje wartość `int`jako .
* Przechowuje wartość `NumberConfig` we właściwości do użycia przez stronę.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren i istnieje

W poniższych przykładach należy wziąć pod uwagę następujący plik JSON. Cztery klucze znajdują się w dwóch sekcjach, z których jedna zawiera parę podsekcji:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Po odczytaniu pliku do konfiguracji tworzone są następujące unikatowe klucze hierarchiczne w celu przechowywania wartości konfiguracji:

* section0:key0
* section0:key1
* sekcja1:key0
* sekcja1:key1
* section2:subsection0:key0
* section2:podsekcja0:key1
* section2:subsection1:key0
* section2:podsekcja1:key1

### <a name="getsection"></a>Getsection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) wyodrębnia podsekcję konfiguracji z określonym kluczem podsekcji.

Aby zwrócić <xref:Microsoft.Extensions.Configuration.IConfigurationSection> zawierającą tylko pary klucz-wartość `section1` `GetSection` w , wywołaj i podaj nazwę sekcji:

```csharp
var configSection = _config.GetSection("section1");
```

Nie `configSection` ma wartości, tylko klucz i ścieżkę.

Podobnie, aby uzyskać wartości dla `section2:subsection0`kluczy w , wywołaj `GetSection` i podaj ścieżkę przekroju:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`nigdy `null`nie zwraca . Jeśli pasująca sekcja nie zostanie `IConfigurationSection` znaleziona, zwracana jest pusta sekcja.

Po `GetSection` zwróceniu <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> pasującej sekcji nie jest wypełniona. A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> i są zwracane, gdy sekcja istnieje.

### <a name="getchildren"></a>Getchildren

Wywołanie [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) `section2` na `IEnumerable<IConfigurationSection>` uzyskuje, który zawiera:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Exists

Użyj [ConfigurationExtensions.Exists,](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) aby ustalić, czy istnieje sekcja konfiguracji:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Biorąc pod uwagę `sectionExists` `false` przykładowe dane, `section2:subsection2` jest to, ponieważ nie ma sekcji w danych konfiguracji.

## <a name="bind-to-an-object-graph"></a>Powiązanie wykresu obiektu

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>jest w stanie powiąże cały wykres obiektów POCO. Podobnie jak w odniesieniu do wiązania prostego obiektu, tylko publiczne właściwości odczytu/zapisu są powiązane.

Przykład zawiera `TvShow` model, którego wykres `Metadata` `Actors` obiektów zawiera i klasy (*Modele/TvShow.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Przykładowa aplikacja ma plik *tvshow.xml* zawierający dane konfiguracyjne:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Konfiguracja jest powiązana z `TvShow` całym `Bind` wykresem obiektu za pomocą metody. Powiązane wystąpienie jest przypisane do właściwości do renderowania:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)wiąże i zwraca określony typ. `Get<T>`jest wygodniejszy `Bind`niż korzystanie z programu . Poniższy kod pokazuje, `Get<T>` jak używać z poprzednim przykładem:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Powiąż tablicę z klasą

*Przykładowa aplikacja demonstruje pojęcia wyjaśnione w tej sekcji.*

Obsługuje <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> tablice powiązania do obiektów przy użyciu indeksów tablicy w kluczach konfiguracyjnych. Każdy format tablicy, który udostępnia`:0:`segment `:1:` &hellip; `:{n}:`klucza numerycznego ( , , ) jest w stanie wiązania tablicy do tablicy klasy POCO.

> [!NOTE]
> Powiązanie jest przewidziane w konwencji. Dostawcy konfiguracji niestandardowej nie są wymagane do zaimplementowania powiązania tablicy.

**Przetwarzanie macierzy w pamięci**

Należy wziąć pod uwagę klucze konfiguracji i wartości pokazane w poniższej tabeli.

| Klucz             | Wartość  |
| :-------------: | :----: |
| tablica:wpisy:0 | wartość0 |
| tablica:wpisy:1 | wartość 1 |
| tablica:wpisy:2 | wartość2 |
| tablica:wpisy:4 | wartość4 |
| tablica:wpisy:5 | wartość5 |

Te klucze i wartości są ładowane w przykładowej aplikacji przy użyciu dostawcy konfiguracji pamięci:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Tablica pomija wartość indeksu &num;3. Spinacz konfiguracji nie jest w stanie powiązać wartości null lub tworzenia wpisów null w obiektach powiązanych, co staje się jasne w momencie, gdy wynik powiązania tej tablicy do obiektu jest pokazany.

W przykładowej aplikacji klasa POCO jest dostępna do przechowywania powiązanych danych konfiguracyjnych:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Dane konfiguracyjne są powiązane z obiektem:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)można również użyć składni, co skutkuje bardziej kompaktowym kodem:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Obiekt powiązany, wystąpienie `ArrayExample`, odbiera dane tablicy z konfiguracji.

| `ArrayExample.Entries`Indeks | `ArrayExample.Entries`Wartość |
| :--------------------------: | :--------------------------: |
| 0                            | wartość0                       |
| 1                            | wartość 1                       |
| 2                            | wartość2                       |
| 3                            | wartość4                       |
| 4                            | wartość5                       |

Indeks &num;3 w obiekcie powiązanym `array:4` przechowuje dane konfiguracyjne klucza konfiguracji i jego wartość `value4`. Gdy dane konfiguracyjne zawierające tablicę są powiązane, indeksy tablicowe w kluczach konfiguracji są używane jedynie do iteracji danych konfiguracji podczas tworzenia obiektu. Wartości null nie można zachować w danych konfiguracyjnych, a wpis o wartości null nie jest tworzony w obiekcie powiązanym, gdy tablica w kluczach konfiguracyjnych pomija jeden lub więcej indeksów.

Brakujący element konfiguracji &num;dla indeksu 3 `ArrayExample` może być dostarczony przed powiązaniem z wystąpieniem przez dowolnego dostawcę konfiguracji, który tworzy poprawną parę klucz-wartość w konfiguracji. Jeśli przykład zawierał dodatkowego dostawcę konfiguracji JSON z brakującą parą klucz-wartość, jest ona `ArrayExample.Entries` zgodna z pełną tablicą konfiguracji:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

W pliku `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Para klucz-wartość pokazana w tabeli jest ładowana do konfiguracji.

| Klucz             | Wartość  |
| :-------------: | :----: |
| tablica:wpisy:3 | wartość3 |

Jeśli `ArrayExample` wystąpienie klasy jest powiązane po dostawcy konfiguracji &num;JSON zawiera `ArrayExample.Entries` wpis dla indeksu 3, tablica zawiera wartość.

| `ArrayExample.Entries`Indeks | `ArrayExample.Entries`Wartość |
| :--------------------------: | :--------------------------: |
| 0                            | wartość0                       |
| 1                            | wartość 1                       |
| 2                            | wartość2                       |
| 3                            | wartość3                       |
| 4                            | wartość4                       |
| 5                            | wartość5                       |

**Przetwarzanie macierzy JSON**

Jeśli plik JSON zawiera tablicę, klucze konfiguracji są tworzone dla elementów tablicy z indeksem sekcji opartej na wartości zerowej. W następującym pliku `subsection` konfiguracyjnym jest tablica:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Dostawca konfiguracji JSON odczytuje dane konfiguracyjne do następujących par klucz-wartość:

| Klucz                     | Wartość  |
| ----------------------- | :----: |
| json_array:key          | wartośćAA |
| json_array:podsekcja:0 | wartośćB |
| json_array:podsekcja:1 | wartośćC |
| json_array:podsekcja:2 | Wyceniane |

W przykładowej aplikacji dostępna jest następująca klasa POCO do powiązania par klucza konfiguracji z wartością:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Po `JsonArrayExample.Key` wiązaniu, `valueA`posiada wartość . Wartości podsekcji są przechowywane we właściwości `Subsection`tablicy POCO, .

| `JsonArrayExample.Subsection`Indeks | `JsonArrayExample.Subsection`Wartość |
| :---------------------------------: | :---------------------------------: |
| 0                                   | wartośćB                              |
| 1                                   | wartośćC                              |
| 2                                   | Wyceniane                              |

## <a name="custom-configuration-provider"></a>Niestandardowy dostawca konfiguracji

Przykładowa aplikacja pokazuje, jak utworzyć podstawowego dostawcę konfiguracji, który odczytuje pary klucza konfiguracji z bazy danych przy użyciu [entity framework (EF)](/ef/core/).

Dostawca posiada następujące cechy:

* Baza danych EF w pamięci jest używana do celów demonstracyjnych. Aby użyć bazy danych, która wymaga `ConfigurationBuilder` ciągu połączenia, należy zaimplementować pomocniczy do poniesienia ciągu połączenia od innego dostawcy konfiguracji.
* Dostawca odczytuje tabelę bazy danych do konfiguracji podczas uruchamiania. Dostawca nie wysyła zapytania do bazy danych na podstawie klucza.
* Ponowne ładowanie na zmianę nie jest zaimplementowana, więc aktualizowanie bazy danych po uruchomieniu aplikacji nie ma wpływu na konfigurację aplikacji.

Zdefiniuj encję `EFConfigurationValue` do przechowywania wartości konfiguracji w bazie danych.

*Modele/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Dodaj `EFConfigurationContext` do przechowywania i dostępu do skonfigurowanych wartości.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Utwórz klasę implementuują . <xref:Microsoft.Extensions.Configuration.IConfigurationSource>

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Utwórz niestandardowego dostawcę <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>konfiguracji, dziedzicząc po programie . Dostawca konfiguracji inicjuje bazę danych, gdy jest pusta.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Metoda `AddEFConfiguration` rozszerzenia umożliwia dodanie źródła `ConfigurationBuilder`konfiguracji do pliku .

*Rozszerzenia/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Poniższy kod pokazuje, jak `EFConfigurationProvider` używać niestandardowych w *Program.cs:*

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Konfiguracja dostępu podczas uruchamiania

Wstrzyknąć `IConfiguration` do konstruktora, `Startup` aby uzyskać dostęp do wartości konfiguracji w `Startup.ConfigureServices`programie . Aby uzyskać `Startup.Configure`dostęp do `IConfiguration` konfiguracji w , albo wstrzyknąć bezpośrednio do metody lub użyć wystąpienia z konstruktora:

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Na przykład uzyskiwania dostępu do konfiguracji przy użyciu metod wygody uruchamiania, zobacz [Uruchamianie aplikacji: Metody wygody](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Uzyskiwanie dostępu do konfiguracji na stronie Razor Pages lub w widoku MVC

Aby uzyskać dostęp do ustawień konfiguracji na stronie Strony Razor lub widoku MVC, dodaj [using dyrektywy](xref:mvc/views/razor#using) [(Odwołanie C#: using directive)](/dotnet/csharp/language-reference/keywords/using-directive)dla [obszaru nazw Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) i wstrzyknąć <xref:Microsoft.Extensions.Configuration.IConfiguration> do strony lub widoku.

Na stronie Strony Maszynki do Golenia:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

W widoku MVC:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Dodawanie konfiguracji z zespołu zewnętrznego

Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umożliwia dodawanie ulepszeń do aplikacji podczas uruchamiania `Startup` z zewnętrznego zestawu poza klasą aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/configuration/options>

::: moniker-end
