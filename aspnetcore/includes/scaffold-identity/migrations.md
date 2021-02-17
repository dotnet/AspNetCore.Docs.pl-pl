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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552409"
---
Wygenerowany Identity kod bazy danych wymaga [migracji Entity Framework Core](/ef/core/managing-schemas/migrations/). Utwórz migrację i zaktualizuj bazę danych. Na przykład uruchom następujące polecenia:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W **konsoli Menedżera pakietów** programu Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

IdentityParametr nazwy "Utwórz schemat" dla `Add-Migration` polecenia jest dowolny. `"CreateIdentitySchema"` zawiera opis migracji.
