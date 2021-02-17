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
<span data-ttu-id="35a92-101">Wygenerowany Identity kod bazy danych wymaga [migracji Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="35a92-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="35a92-102">Utwórz migrację i zaktualizuj bazę danych.</span><span class="sxs-lookup"><span data-stu-id="35a92-102">Create a migration and update the database.</span></span> <span data-ttu-id="35a92-103">Na przykład uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="35a92-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="35a92-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="35a92-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="35a92-105">W **konsoli Menedżera pakietów** programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="35a92-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="35a92-106">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="35a92-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="35a92-107">IdentityParametr nazwy "Utwórz schemat" dla `Add-Migration` polecenia jest dowolny.</span><span class="sxs-lookup"><span data-stu-id="35a92-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="35a92-108">`"CreateIdentitySchema"` zawiera opis migracji.</span><span class="sxs-lookup"><span data-stu-id="35a92-108">`"CreateIdentitySchema"` describes the migration.</span></span>
