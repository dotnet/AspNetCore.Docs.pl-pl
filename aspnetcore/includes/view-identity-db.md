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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551902"
---
### <a name="view-the-identity-database"></a>Wyświetlanie Identity bazy danych

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* Z menu **Widok** wybierz pozycję **Eksplorator obiektów SQL Server** (SSOX).
* Przejdź do **(LocalDB) MSSQLLocalDB (SQL Server 13)**. Kliknij prawym przyciskiem myszy obiekt **dbo. AspNetUsers**  >  **dane widoku**:

![Menu kontekstowe w tabeli AspNetUsers w Eksplorator obiektów SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Istnieje wiele narzędzi innych firm, które można pobrać, aby zarządzać bazą danych oprogramowania SQLite i wyświetlać ją, na przykład [przeglądarkę bazy danych dla oprogramowania SQLite](https://sqlitebrowser.org/).

---