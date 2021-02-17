---
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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552407"
---
::: moniker range=">= aspnetcore-3.0"

Uruchamianie Identity szkieletu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **Identity** > **Dodaj**.
* W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu, aby plik układu nie został zastąpiony nieprawidłowym znacznikiem. Gdy zostanie wybrany istniejący plik *\_ Layout. cshtml* , **nie** zostanie on nadpisany. Na przykład:
    * `~/Pages/Shared/_Layout.cshtml` dla Razor stron lub Blazor Server projektów z istniejącą Razor infrastrukturą stron
    * `~/Views/Shared/_Layout.cshtml` dla projektów MVC lub Blazor Server projektów z istniejącą infrastrukturą MVC
* Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia. Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.
  * Wybierz klasę kontekstu danych.
  * Wybierz pozycję **Dodaj**.
* Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla Identity :
  * Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**. Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).
  * Wybierz pozycję **Dodaj**.

Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Dodaj wymagane odwołania do pakietu NuGet do pliku projektu (*. csproj*). Uruchom następujące polecenia w katalogu projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie. Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach. Na przykład:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

W przypadku uruchomienia Identity szkieletu bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne Identity strony interfejsu użytkownika zostaną utworzone w projekcie.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Uruchamianie Identity szkieletu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **Identity** > **Dodaj**.
* W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem. Gdy zostanie wybrany istniejący plik *\_ Layout. cshtml* , **nie** zostanie on nadpisany. Na przykład:
    * `~/Pages/Shared/_Layout.cshtml` dla Razor stron
    * `~/Views/Shared/_Layout.cshtml` dla projektów MVC
* Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia. Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.
  * Wybierz klasę kontekstu danych.
  * Wybierz pozycję **Dodaj**.
* Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla Identity :
  * Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**. Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).
  * Wybierz pozycję **Dodaj**.

Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Dodaj odwołanie do pakietu [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (*. csproj*). Uruchom następujące polecenia w katalogu projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie. Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach. Na przykład:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

W przypadku uruchomienia Identity szkieletu bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne Identity strony interfejsu użytkownika zostaną utworzone w projekcie.

---

::: moniker-end
