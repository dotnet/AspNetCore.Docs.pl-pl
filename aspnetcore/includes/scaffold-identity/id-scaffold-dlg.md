Uruchom szkielet tożsamości:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj**  >  **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodaj nowy element szkieletowy** wybierz pozycję **Identity**  >  **Dodaj**tożsamość.
* W oknie dialogowym **Dodawanie tożsamości** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem:
    * `~/Pages/Shared/_Layout.cshtml`dla Razor Pages
    * `~/Views/Shared/_Layout.cshtml`dla projektów MVC
    * Aplikacje serwera Blazor utworzone na podstawie szablonu serwera Blazor ( `blazorserver` ) nie są domyślnie skonfigurowane dla Razor Pages lub MVC. Pozostaw pusty wpis strony układu.
  * Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**. Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).
* Wybierz pozycję **Dodaj**.

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

W folderze projektu uruchom program do tworzenia szkieletu tożsamości z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
