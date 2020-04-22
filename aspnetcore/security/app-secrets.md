---
title: Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje jako wpisy tajne aplikacji podczas tworzenia aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791578"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpieczne przechowywanie tajemnic aplikacji w rozwoju w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Rick [Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim. Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie źródłowym. Wpisy tajne produkcji nie powinny być używane do programowania lub testowania. Wpisy tajne nie powinny być wdrażane z aplikacją. Zamiast tego wpisy tajne powinny być udostępniane w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, usługa Azure Key Vault itp. Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe są używane w celu uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracyjnych. Zmienne środowiskowe zastępują wartości konfiguracji dla wszystkich wcześniej określonych źródeł konfiguracji.

Należy wziąć pod uwagę ASP.NET podstawowej aplikacji sieci web, w której zabezpieczenia **poszczególnych kont użytkowników** jest włączona. Domyślny ciąg połączenia bazy danych znajduje się w pliku *appsettings.json* projektu z kluczem `DefaultConnection`. Domyślny ciąg połączenia jest dla LocalDB, który działa w trybie użytkownika i nie wymaga hasła. Podczas wdrażania aplikacji `DefaultConnection` wartość klucza można zastąpić wartością zmiennej środowiskowej. Zmienna środowiskowa może przechowywać pełny ciąg połączenia z poufnymi poświadczeniami.

> [!WARNING]
> Zmienne środowiskowe są zazwyczaj przechowywane w zwykłym, niezaszyfrowanym tekście. Jeśli maszyna lub proces zostanie naruszona, zmienne środowiskowe są dostępne dla niezaufanych stron. Mogą być wymagane dodatkowe środki zapobiegające ujawnieniu informacji o tajemnicach użytkownika.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Tajny menedżer

Narzędzie Secret Manager przechowuje poufne dane podczas opracowywania projektu ASP.NET Core. W tym kontekście fragment poufnych danych jest kluczem tajnym aplikacji. Wpisy tajne aplikacji są przechowywane w osobnej lokalizacji od drzewa projektu. Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w kilku projektach. Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.

> [!WARNING]
> Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinno być traktowane jako zaufany magazyn. To tylko do celów rozwojowych. Klucze i wartości są przechowywane w pliku konfiguracyjnym JSON w katalogu profilu użytkownika.

## <a name="how-the-secret-manager-tool-works"></a>Jak działa narzędzie Secret Manager

Narzędzie Secret Manager odsuń szczegóły implementacji, takie jak gdzie i jak wartości są przechowywane. Można użyć narzędzia bez znajomości tych szczegółów implementacji. Wartości są przechowywane w pliku konfiguracyjnym JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:

# <a name="windows"></a>[Windows](#tab/windows)

Ścieżka systemu plików:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ścieżka systemu plików:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

W poprzednich ścieżkach plików `<user_secrets_id>` zastąp wartością `UserSecretsId` określoną w pliku *csproj.*

Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisanych za pomocą narzędzia Menedżer tajny. Te szczegóły implementacji mogą ulec zmianie. Na przykład wartości tajne nie są szyfrowane, ale może być w przyszłości.

## <a name="enable-secret-storage"></a>Włączanie pamięci masowej tajnej

Narzędzie Secret Manager działa na ustawieniach konfiguracji specyficznych dla projektu przechowywanych w profilu użytkownika.

Narzędzie Secret Manager `init` zawiera polecenie w pliku .NET Core SDK 3.0.100 lub nowszym. Aby użyć wpisów tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:

```dotnetcli
dotnet user-secrets init
```

Poprzednie polecenie dodaje `UserSecretsId` element w `PropertyGroup` pliku *csproj.* Domyślnie wewnętrzny tekst `UserSecretsId` jest identyfikatorem GUID. Tekst wewnętrzny jest dowolny, ale jest unikatowy dla projektu.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj wpisami tajnymi użytkowników** z menu kontekstowego. Ten gest `UserSecretsId` dodaje element wypełniony identyfikatorem GUID do pliku *csproj.*

## <a name="set-a-secret"></a>Ustawianie klucza tajnego

Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości. Klucz tajny jest skojarzony z `UserSecretsId` wartością projektu. Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

W poprzednim przykładzie dwukropek oznacza, że `Movies` jest obiektem dosłownym z właściwością. `ServiceApiKey`

Narzędzie Secret Manager może być również używane z innych katalogów. Użyj `--project` tej opcji, aby podać ścieżkę systemu plików, przy której istnieje plik *csproj.* Przykład:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Spłaszczanie struktury JSON w programie Visual Studio

Gest Zarządzanie **wpisami tajnymi użytkowników** w programie Visual Studio otwiera plik *secrets.json* w edytorze tekstu. Zastąp zawartość *pliku secrets.json* parami klucz-wartość, które mają być przechowywane. Przykład:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON jest spłaszczana po `dotnet user-secrets remove` `dotnet user-secrets set`modyfikacjach za pośrednictwem lub . Na przykład `dotnet user-secrets remove "Movies:ConnectionString"` uruchamianie `Movies` zwija dosłowny obiekt. Zmodyfikowany plik przypomina następującą:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Ustawianie wielu wpisów tajnych

Partia wpisów tajnych można ustawić przez `set` rurociągów JSON do polecenia. W poniższym przykładzie zawartość pliku *input.json* są `set` potokami do polecenia.

# <a name="windows"></a>[Windows](#tab/windows)

Otwórz powłokę polecenia i wykonaj następujące polecenie:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Otwórz powłokę polecenia i wykonaj następujące polecenie:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Dostęp do tajemnicy

[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do tajnych wpisów tajnych menedżera.

Źródło konfiguracji wpisów tajnych użytkowników jest automatycznie <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> dodawane w trybie programowania, gdy projekt wywołuje zainicjowanie nowego wystąpienia hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi. `CreateDefaultBuilder`połączenia, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> gdy <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>jest:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Gdy `CreateDefaultBuilder` nie jest wywoływana, dodaj źródło konfiguracji <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>wpisów tajnych użytkownika jawnie, wywołując program . Wywołaj `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w poniższym przykładzie:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Wpisy tajne użytkownika można `Configuration` pobrać za pośrednictwem interfejsu API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapowanie wpisów tajnych do poco

Mapowanie całego obiektu literału do POCO (prosta klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Aby zamapować poprzednie wpisy tajne `Configuration` do poco, należy użyć funkcji [powiązania wykresu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API. Następujący kod wiąże się `MovieSettings` z niestandardowym poco i uzyskuje dostęp do wartości `ServiceApiKey` właściwości:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` Wpisy `Movies:ServiceApiKey` tajne są mapowane do `MovieSettings`odpowiednich właściwości w:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Wymiana ciągów z wpisami tajnymi

Przechowywanie haseł w postaci zwykłego tekstu jest niezabezpieczone. Na przykład parametry połączenia bazy danych przechowywane w *appsettings.json* może zawierać hasło dla określonego użytkownika:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpieczniejszym podejściem jest przechowywanie hasła jako klucza tajnego. Przykład:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Usuń `Password` parę klucz-wartość z ciągu połączenia w *pliku appsettings.json*. Przykład:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Wartość klucza tajnego można <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ustawić na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> właściwość obiektu, aby zakończyć parametry połączenia:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Lista wpisów tajnych

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets list
```

Zostaną wyświetlone następujące dane wyjściowe:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

W poprzednim przykładzie dwukropek w nazwach głównych oznacza hierarchię obiektów w pliku *secrets.json*.

## <a name="remove-a-single-secret"></a>Usuwanie pojedynczego klucza tajnego

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Plik *secrets.json* aplikacji został zmodyfikowany w celu usunięcia pary klucz-wartość skojarzonej z kluczem: `MoviesConnectionString`

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`wyświetla następujący komunikat:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Usuń wszystkie wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets clear
```

Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z pliku *secrets.json:*

```json
{}
```

Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* Zobacz [ten problem,](https://github.com/dotnet/AspNetCore.Docs/issues/16328) aby uzyskać informacje na temat uzyskiwania dostępu do tajnego menedżera z usług IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Rick [Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i Scott [Addie](https://github.com/scottaddie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

W tym dokumencie opisano techniki przechowywania i pobierania poufnych danych podczas opracowywania aplikacji ASP.NET Core na komputerze deweloperskim. Nigdy nie przechowuj haseł ani innych poufnych danych w kodzie źródłowym. Wpisy tajne produkcji nie powinny być używane do programowania lub testowania. Wpisy tajne nie powinny być wdrażane z aplikacją. Zamiast tego wpisy tajne powinny być udostępniane w środowisku produkcyjnym za pomocą kontrolowanych środków, takich jak zmienne środowiskowe, usługa Azure Key Vault itp. Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe są używane w celu uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracyjnych. Zmienne środowiskowe zastępują wartości konfiguracji dla wszystkich wcześniej określonych źródeł konfiguracji.

Należy wziąć pod uwagę ASP.NET podstawowej aplikacji sieci web, w której zabezpieczenia **poszczególnych kont użytkowników** jest włączona. Domyślny ciąg połączenia bazy danych znajduje się w pliku *appsettings.json* projektu z kluczem `DefaultConnection`. Domyślny ciąg połączenia jest dla LocalDB, który działa w trybie użytkownika i nie wymaga hasła. Podczas wdrażania aplikacji `DefaultConnection` wartość klucza można zastąpić wartością zmiennej środowiskowej. Zmienna środowiskowa może przechowywać pełny ciąg połączenia z poufnymi poświadczeniami.

> [!WARNING]
> Zmienne środowiskowe są zazwyczaj przechowywane w zwykłym, niezaszyfrowanym tekście. Jeśli maszyna lub proces zostanie naruszona, zmienne środowiskowe są dostępne dla niezaufanych stron. Mogą być wymagane dodatkowe środki zapobiegające ujawnieniu informacji o tajemnicach użytkownika.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Tajny menedżer

Narzędzie Secret Manager przechowuje poufne dane podczas opracowywania projektu ASP.NET Core. W tym kontekście fragment poufnych danych jest kluczem tajnym aplikacji. Wpisy tajne aplikacji są przechowywane w osobnej lokalizacji od drzewa projektu. Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w kilku projektach. Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.

> [!WARNING]
> Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinno być traktowane jako zaufany magazyn. To tylko do celów rozwojowych. Klucze i wartości są przechowywane w pliku konfiguracyjnym JSON w katalogu profilu użytkownika.

## <a name="how-the-secret-manager-tool-works"></a>Jak działa narzędzie Secret Manager

Narzędzie Secret Manager odsuń szczegóły implementacji, takie jak gdzie i jak wartości są przechowywane. Można użyć narzędzia bez znajomości tych szczegółów implementacji. Wartości są przechowywane w pliku konfiguracyjnym JSON w folderze profilu użytkownika chronionego przez system na komputerze lokalnym:

# <a name="windows"></a>[Windows](#tab/windows)

Ścieżka systemu plików:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Ścieżka systemu plików:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

W poprzednich ścieżkach plików `<user_secrets_id>` zastąp wartością `UserSecretsId` określoną w pliku *csproj.*

Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisanych za pomocą narzędzia Menedżer tajny. Te szczegóły implementacji mogą ulec zmianie. Na przykład wartości tajne nie są szyfrowane, ale może być w przyszłości.

## <a name="enable-secret-storage"></a>Włączanie pamięci masowej tajnej

Narzędzie Secret Manager działa na ustawieniach konfiguracji specyficznych dla projektu przechowywanych w profilu użytkownika.

Aby użyć wpisów `UserSecretsId` tajnych `PropertyGroup` użytkownika, zdefiniuj element w pliku *csproj.* Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu. Deweloperzy zazwyczaj generują identyfikator `UserSecretsId`GUID dla pliku .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz polecenie **Zarządzaj wpisami tajnymi użytkowników** z menu kontekstowego. Ten gest `UserSecretsId` dodaje element wypełniony identyfikatorem GUID do pliku *csproj.*

## <a name="set-a-secret"></a>Ustawianie klucza tajnego

Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości. Klucz tajny jest skojarzony z `UserSecretsId` wartością projektu. Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

W poprzednim przykładzie dwukropek oznacza, że `Movies` jest obiektem dosłownym z właściwością. `ServiceApiKey`

Narzędzie Secret Manager może być również używane z innych katalogów. Użyj `--project` tej opcji, aby podać ścieżkę systemu plików, przy której istnieje plik *csproj.* Przykład:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Spłaszczanie struktury JSON w programie Visual Studio

Gest Zarządzanie **wpisami tajnymi użytkowników** w programie Visual Studio otwiera plik *secrets.json* w edytorze tekstu. Zastąp zawartość *pliku secrets.json* parami klucz-wartość, które mają być przechowywane. Przykład:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON jest spłaszczana po `dotnet user-secrets remove` `dotnet user-secrets set`modyfikacjach za pośrednictwem lub . Na przykład `dotnet user-secrets remove "Movies:ConnectionString"` uruchamianie `Movies` zwija dosłowny obiekt. Zmodyfikowany plik przypomina następującą:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Ustawianie wielu wpisów tajnych

Partia wpisów tajnych można ustawić przez `set` rurociągów JSON do polecenia. W poniższym przykładzie zawartość pliku *input.json* są `set` potokami do polecenia.

# <a name="windows"></a>[Windows](#tab/windows)

Otwórz powłokę polecenia i wykonaj następujące polecenie:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Otwórz powłokę polecenia i wykonaj następujące polecenie:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Dostęp do tajemnicy

[Interfejs API konfiguracji ASP.NET Core](xref:fundamentals/configuration/index) zapewnia dostęp do tajnych wpisów tajnych menedżera.

Jeśli projekt jest przeznaczony dla platformy .NET Framework, zainstaluj pakiet [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.

W ASP.NET Core 2.0 lub nowszym źródło konfiguracji wpisów tajnych użytkowników <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> jest automatycznie dodawane w trybie rozwoju, gdy projekt wywołuje zainicjowanie nowego wystąpienia hosta ze wstępnie skonfigurowanymi ustawieniami domyślnymi. `CreateDefaultBuilder`połączenia, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> gdy <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>jest:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Gdy `CreateDefaultBuilder` nie jest wywoływana, dodaj źródło konfiguracji <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wpisów `Startup` tajnych użytkownika jawnie wywołując w konstruktorze. Wywołaj `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w poniższym przykładzie:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Wpisy tajne użytkownika można `Configuration` pobrać za pośrednictwem interfejsu API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapowanie wpisów tajnych do poco

Mapowanie całego obiektu literału do POCO (prosta klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Aby zamapować poprzednie wpisy tajne `Configuration` do poco, należy użyć funkcji [powiązania wykresu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API. Następujący kod wiąże się `MovieSettings` z niestandardowym poco i uzyskuje dostęp do wartości `ServiceApiKey` właściwości:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` Wpisy `Movies:ServiceApiKey` tajne są mapowane do `MovieSettings`odpowiednich właściwości w:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Wymiana ciągów z wpisami tajnymi

Przechowywanie haseł w postaci zwykłego tekstu jest niezabezpieczone. Na przykład parametry połączenia bazy danych przechowywane w *appsettings.json* może zawierać hasło dla określonego użytkownika:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bezpieczniejszym podejściem jest przechowywanie hasła jako klucza tajnego. Przykład:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Usuń `Password` parę klucz-wartość z ciągu połączenia w *pliku appsettings.json*. Przykład:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Wartość klucza tajnego można <xref:System.Data.SqlClient.SqlConnectionStringBuilder> ustawić na <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> właściwość obiektu, aby zakończyć parametry połączenia:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Lista wpisów tajnych

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets list
```

Zostaną wyświetlone następujące dane wyjściowe:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

W poprzednim przykładzie dwukropek w nazwach głównych oznacza hierarchię obiektów w pliku *secrets.json*.

## <a name="remove-a-single-secret"></a>Usuwanie pojedynczego klucza tajnego

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Plik *secrets.json* aplikacji został zmodyfikowany w celu usunięcia pary klucz-wartość skojarzonej z kluczem: `MoviesConnectionString`

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Usuń wszystkie wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik *csproj:*

```dotnetcli
dotnet user-secrets clear
```

Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z pliku *secrets.json:*

```json
{}
```

Uruchomiony `dotnet user-secrets list` wyświetla następujący komunikat:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* Zobacz [ten problem,](https://github.com/dotnet/AspNetCore.Docs/issues/16328) aby uzyskać informacje na temat uzyskiwania dostępu do tajnego menedżera z usług IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end