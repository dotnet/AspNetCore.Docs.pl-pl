---
title: Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak przechowywać i pobierać poufne informacje podczas opracowywania aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc, contperfq2
ms.date: 11/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035856"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Bezpieczne przechowywanie wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

W tym dokumencie wyjaśniono, jak zarządzać danymi poufnymi dla aplikacji ASP.NET Core na komputerze deweloperskim. Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym. Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych. Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji. Zamiast tego należy uzyskać dostęp do wpisów tajnych produkcji przy użyciu kontrolowanych środków, takich jak zmienne środowiskowe lub Azure Key Vault. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji. Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.

Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** . Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` . Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła. Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej. Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.

> [!WARNING]
> Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu. W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron. Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Menedżer wpisów tajnych

Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core. W tym kontekście dane poufne są tajne dla aplikacji. Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu. Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach. Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.

> [!WARNING]
> Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn. Jest przeznaczona tylko do celów programistycznych. Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.

## <a name="how-the-secret-manager-tool-works"></a>Jak działa narzędzie do zarządzania kluczami tajnymi

Narzędzie Secret Manager ukrywa szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości. Możesz użyć narzędzia bez znajomości tych szczegółów implementacji. Wartości są przechowywane w pliku JSON w folderze profilu użytkownika komputera lokalnego:

# <a name="windows"></a>[Windows](#tab/windows)

Ścieżka systemu plików:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ścieżka systemu plików:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku projektu.

Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi. Te szczegóły implementacji mogą ulec zmianie. Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.

## <a name="enable-secret-storage"></a>Włącz magazyn tajny

Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.

Narzędzie Secret Manager zawiera `init` polecenie w zestaw .NET Core SDK 3.0.100 lub nowszym. Aby użyć kluczy tajnych użytkownika, uruchom następujące polecenie w katalogu projektu:

```dotnetcli
dotnet user-secrets init
```

Poprzednie polecenie dodaje `UserSecretsId` element w `PropertyGroup` pliku projektu. Domyślnie tekst wewnętrzny `UserSecretsId` jest identyfikatorem GUID. Wewnętrzny tekst jest dowolny, ale jest unikatowy dla projektu.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego. Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku projektu.

## <a name="set-a-secret"></a>Ustaw klucz tajny

Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości. Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` . Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.

Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów. Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której istnieje plik projektu. Przykład:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Spłaszczanie struktury JSON w programie Visual Studio

Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów. Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane. Przykład:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` . Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu. Zmodyfikowany plik przypomina następujący kod JSON:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Ustawianie wielu wpisów tajnych

Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia. W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.

# <a name="windows"></a>[Windows](#tab/windows)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Dostęp do klucza tajnego

Aby uzyskać dostęp do wpisu tajnego, wykonaj następujące czynności:

1. [Rejestrowanie źródła konfiguracji kluczy tajnych użytkownika](#register-the-user-secrets-configuration-source)
1. [Odczytywanie wpisu tajnego za pośrednictwem interfejsu API konfiguracji](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>Rejestrowanie źródła konfiguracji kluczy tajnych użytkownika

[Dostawca konfiguracji](/dotnet/core/extensions/configuration-providers) kluczy tajnych użytkownika rejestruje odpowiednie źródło konfiguracji za pomocą [interfejsu API konfiguracji](xref:fundamentals/configuration/index)platformy .NET.

Źródło konfiguracji kluczy tajnych użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt jest wywoływany <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> . `CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> jest <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie przez wywołanie metody <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> . Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>Odczytywanie wpisu tajnego za pośrednictwem interfejsu API konfiguracji

Jeśli źródło konfiguracji kluczy tajnych użytkownika zostanie zarejestrowane, interfejs API konfiguracji platformy .NET może odczytać wpisy tajne. [Iniekcja konstruktora](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) może służyć do uzyskania dostępu do interfejsu API konfiguracji platformy .NET. Rozważ następujące przykłady odczytywania `Movies:ServiceApiKey` klucza:

**Klasa początkowa:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor Model strony stron:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

Aby uzyskać więcej informacji, zobacz [Konfiguracja dostępu w](xref:fundamentals/configuration/index#access-configuration-in-startup) [konfiguracjach uruchamiania i dostępu na Razor stronach](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).

## <a name="map-secrets-to-a-poco"></a>Mapuj wpisy tajne do POCO

Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Aby zmapować poprzednie wpisy tajne do POCO, użyj funkcji [powiązania grafu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API konfiguracji platformy .NET. Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zastępowanie ciągów hasłami tajnymi

Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne. Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego. Przykład:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* . Przykład:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Wystaw wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets list
```

Wyświetlane są następujące dane wyjściowe:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna*.

## <a name="remove-a-single-secret"></a>Usuń pojedynczy klucz tajny

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` wyświetla następujący komunikat:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Usuń wszystkie wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets clear
```

Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:

```json
{}
```

Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do kluczy tajnych użytkownika z usług IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)i [Scott Addie](https://github.com/scottaddie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

W tym dokumencie wyjaśniono, jak zarządzać danymi poufnymi dla aplikacji ASP.NET Core na komputerze deweloperskim. Nie należy przechowywać haseł ani innych poufnych danych w kodzie źródłowym. Tajemnice produkcyjne nie powinny być używane do celów deweloperskich i testowych. Wpisy tajne nie powinny być wdrażane przy użyciu aplikacji. Zamiast tego należy uzyskać dostęp do wpisów tajnych produkcji przy użyciu kontrolowanych środków, takich jak zmienne środowiskowe lub Azure Key Vault. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe służą do uniknięcia przechowywania wpisów tajnych aplikacji w kodzie lub w lokalnych plikach konfiguracji. Zmienne środowiskowe przesłaniają wartości konfiguracyjne dla wszystkich poprzednio określonych źródeł konfiguracji.

Rozważ ASP.NET Core aplikacji sieci Web, w której włączono zabezpieczenia **poszczególnych kont użytkowników** . Domyślne parametry połączenia z bazą danych są zawarte w pliku projektu *appsettings.json* z kluczem `DefaultConnection` . Domyślne parametry połączenia to LocalDB, które są uruchamiane w trybie użytkownika i nie wymagają hasła. Podczas wdrażania aplikacji `DefaultConnection` wartość klucza może być zastąpiona wartością zmiennej środowiskowej. Zmienna środowiskowa może przechowywać kompletne parametry połączenia z poufnymi poświadczeniami.

> [!WARNING]
> Zmienne środowiskowe są zwykle przechowywane w postaci zwykłego, nieszyfrowanego tekstu. W przypadku naruszenia zabezpieczeń komputera lub procesu zmienne środowiskowe są dostępne dla niezaufanych stron. Mogą być wymagane dodatkowe środki, które uniemożliwiają ujawnienie kluczy tajnych użytkownika.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Menedżer wpisów tajnych

Narzędzie Secret Manager zapisuje poufne dane podczas opracowywania projektu ASP.NET Core. W tym kontekście dane poufne są tajne dla aplikacji. Wpisy tajne aplikacji są przechowywane w oddzielnej lokalizacji w drzewie projektu. Wpisy tajne aplikacji są skojarzone z określonym projektem lub udostępniane w wielu projektach. Wpisy tajne aplikacji nie są sprawdzane w kontroli źródła.

> [!WARNING]
> Narzędzie Secret Manager nie szyfruje przechowywanych wpisów tajnych i nie powinna być traktowana jako zaufany magazyn. Jest przeznaczona tylko do celów programistycznych. Klucze i wartości są przechowywane w pliku konfiguracji JSON w katalogu profilu użytkownika.

## <a name="how-the-secret-manager-tool-works"></a>Jak działa narzędzie do zarządzania kluczami tajnymi

Narzędzie Secret Manager ukrywa szczegóły implementacji, takie jak miejsce i sposób przechowywania wartości. Możesz użyć narzędzia bez znajomości tych szczegółów implementacji. Wartości są przechowywane w pliku JSON w folderze profilu użytkownika komputera lokalnego:

# <a name="windows"></a>[Windows](#tab/windows)

Ścieżka systemu plików:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ścieżka systemu plików:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

W poprzednich ścieżkach plików Zamień na `<user_secrets_id>` `UserSecretsId` wartość określoną w pliku projektu.

Nie pisz kodu, który zależy od lokalizacji lub formatu danych zapisywanych za pomocą narzędzia do zarządzania kluczami tajnymi. Te szczegóły implementacji mogą ulec zmianie. Na przykład wartości tajne nie są szyfrowane, ale mogą być w przyszłości.

## <a name="enable-secret-storage"></a>Włącz magazyn tajny

Narzędzie Secret Manager działa na specyficznych dla projektu ustawieniach konfiguracji przechowywanych w profilu użytkownika.

Aby użyć kluczy tajnych użytkownika, zdefiniuj `UserSecretsId` element w `PropertyGroup` pliku projektu. Wewnętrzny tekst `UserSecretsId` jest dowolny, ale jest unikatowy dla projektu. Deweloperzy zwykle generują identyfikator GUID dla `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> W programie Visual Studio kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika** z menu kontekstowego. Ten gest dodaje `UserSecretsId` element z identyfikatorem GUID do pliku projektu.

## <a name="set-a-secret"></a>Ustaw klucz tajny

Zdefiniuj klucz tajny aplikacji składający się z klucza i jego wartości. Wpis tajny jest skojarzony z wartością projektu `UserSecretsId` . Na przykład uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

W poprzednim przykładzie dwukropek wskazuje, że `Movies` jest to literał obiektu z `ServiceApiKey` właściwością.

Narzędzia do zarządzania kluczami tajnymi można również użyć z innych katalogów. Użyj `--project` opcji, aby podać ścieżkę systemu plików, w której istnieje plik projektu. Przykład:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Spłaszczanie struktury JSON w programie Visual Studio

Gest **Zarządzanie kluczami tajnymi użytkownika** programu Visual Studio otwiera *secrets.js* w pliku w edytorze tekstów. Zastąp zawartość *secrets.js* przy użyciu par klucz-wartość, które mają być przechowywane. Przykład:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Struktura JSON jest spłaszczona po modyfikacji za pośrednictwem `dotnet user-secrets remove` lub `dotnet user-secrets set` . Na przykład, uruchomione `dotnet user-secrets remove "Movies:ConnectionString"` zwijanie `Movies` literału obiektu. Zmodyfikowany plik przypomina następujący kod JSON:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Ustawianie wielu wpisów tajnych

Partia wpisów tajnych można ustawić za pomocą formatu JSON dla `set` polecenia. W poniższym przykładzie *input.js* zawartości pliku są potoku do `set` polecenia.

# <a name="windows"></a>[Windows](#tab/windows)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Otwórz powłokę poleceń i wykonaj następujące polecenie:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Dostęp do klucza tajnego

[Interfejs API konfiguracji](xref:fundamentals/configuration/index) zapewnia dostęp do kluczy tajnych użytkownika.

Jeśli projekt jest przeznaczony .NET Framework, zainstaluj [Microsoft.Extensions.Configwersja. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Pakiet NuGet UserSecrets.

W ASP.NET Core 2,0 lub nowszej Źródło konfiguracji użytkownika jest automatycznie dodawane w trybie programistycznym, gdy projekt jest wywoływany <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> . `CreateDefaultBuilder` wywołuje <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> się, gdy <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> jest <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Gdy `CreateDefaultBuilder` nie jest wywoływana, Dodaj źródło konfiguracji kluczy tajnych użytkownika jawnie, wywołując <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> w `Startup` konstruktorze. Wywołanie `AddUserSecrets` tylko wtedy, gdy aplikacja działa w środowisku deweloperskim, jak pokazano w następującym przykładzie:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Wpisy tajne użytkownika można pobrać za pośrednictwem interfejsu API konfiguracji platformy .NET:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Mapuj wpisy tajne do POCO

Mapowanie całego literału obiektu na POCO (prosta Klasa .NET z właściwościami) jest przydatne do agregowania powiązanych właściwości.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Aby zmapować poprzednie wpisy tajne do POCO, użyj funkcji [powiązania grafu obiektu](xref:fundamentals/configuration/index#bind-to-an-object-graph) interfejsu API konfiguracji platformy .NET. Następujący kod wiąże się z niestandardowym `MovieSettings` POCO i uzyskuje dostęp do `ServiceApiKey` wartości właściwości:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Wpisy `Movies:ConnectionString` `Movies:ServiceApiKey` tajne i są mapowane na odpowiednie właściwości w programie `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zastępowanie ciągów hasłami tajnymi

Przechowywanie haseł w postaci zwykłego tekstu jest niebezpieczne. Na przykład parametry połączenia z bazą danych przechowywane w programie *appsettings.json* mogą zawierać hasło dla określonego użytkownika:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Bardziej bezpiecznym podejściem jest przechowywanie hasła jako klucza tajnego. Przykład:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Usuń `Password` parę klucz-wartość z parametrów połączenia w *appsettings.json* . Przykład:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Wartość wpisu tajnego można ustawić we <xref:System.Data.SqlClient.SqlConnectionStringBuilder> właściwości obiektu, <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> Aby zakończyć parametry połączenia:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Wystaw wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets list
```

Wyświetlane są następujące dane wyjściowe:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

W poprzednim przykładzie dwukropek w nazwach kluczy oznacza hierarchię obiektów w *secrets.jsna*.

## <a name="remove-a-single-secret"></a>Usuń pojedynczy klucz tajny

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

*secrets.jsaplikacji w* pliku został zmodyfikowany, aby usunąć parę klucz-wartość skojarzoną z `MoviesConnectionString` kluczem:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Usuń wszystkie wpisy tajne

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Uruchom następujące polecenie z katalogu, w którym istnieje plik projektu:

```dotnetcli
dotnet user-secrets clear
```

Wszystkie wpisy tajne użytkownika dla aplikacji zostały usunięte z *secrets.jsw* pliku:

```json
{}
```

Uruchomiony program `dotnet user-secrets list` wyświetla następujący komunikat:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16328) , aby uzyskać informacje na temat uzyskiwania dostępu do kluczy tajnych użytkownika z usług IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end