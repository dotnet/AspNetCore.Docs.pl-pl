::: moniker range=">= aspnetcore-3.0"

Uruchom szkielet tożsamości:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję Dodaj **tożsamość** > **Add**.
* W oknie dialogowym **Dodawanie tożsamości** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu, aby plik układu nie został zastąpiony nieprawidłowym znacznikiem. Gdy zostanie wybrany istniejący plik * \_ Layout. cshtml* , **nie** zostanie on nadpisany. Przykład:
    * `~/Pages/Shared/_Layout.cshtml`w przypadku Razor Pages lub projektów serwera Blazor z istniejącą infrastrukturą Razor Pages
    * `~/Views/Shared/_Layout.cshtml`w przypadku projektów MVC lub projektów serwera Blazor z istniejącą infrastrukturą MVC
* Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia. Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.
  * Wybierz klasę kontekstu danych.
  * Wybierz pozycję **Dodaj**.
* Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla tożsamości:
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

Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

W folderze projektu uruchom program do tworzenia szkieletu tożsamości z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie. Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach. Przykład:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

W przypadku uruchomienia szkieletu tożsamości bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne strony interfejsu użytkownika tożsamości zostaną utworzone w projekcie.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Uruchom szkielet tożsamości:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję Dodaj **tożsamość** > **Add**.
* W oknie dialogowym **Dodawanie tożsamości** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem. Gdy zostanie wybrany istniejący plik * \_ Layout. cshtml* , **nie** zostanie on nadpisany. Przykład:
    * `~/Pages/Shared/_Layout.cshtml`dla Razor Pages
    * `~/Views/Shared/_Layout.cshtml`dla projektów MVC
* Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia. Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.
  * Wybierz klasę kontekstu danych.
  * Wybierz pozycję **Dodaj**.
* Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla tożsamości:
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

Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

W folderze projektu uruchom program do tworzenia szkieletu tożsamości z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie. Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach. Przykład:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

W przypadku uruchomienia szkieletu tożsamości bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne strony interfejsu użytkownika tożsamości zostaną utworzone w projekcie.

---

::: moniker-end
