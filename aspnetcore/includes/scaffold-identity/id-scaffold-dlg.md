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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552027"
---
Uruchamianie Identity szkieletu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj**  >  **nowy element szkieletowy**.
* W lewym okienku okna dialogowego **Dodaj nowy element szkieletowy** wybierz pozycję **Identity**  >  **Dodaj**.
* W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.
  * Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem:
    * `~/Pages/Shared/_Layout.cshtml` dla Razor stron
    * `~/Views/Shared/_Layout.cshtml` dla projektów MVC
    * Blazor Server aplikacje utworzone na podstawie Blazor Server szablonu ( `blazorserver` ) nie są domyślnie skonfigurowane dla Razor stron lub MVC. Pozostaw pusty wpis strony układu.
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

Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami. Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
