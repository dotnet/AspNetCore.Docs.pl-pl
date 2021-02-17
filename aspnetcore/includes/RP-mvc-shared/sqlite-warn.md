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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552164"
---
> [!NOTE]
> <span data-ttu-id="497d3-101">W tym samouczku użyjesz funkcji *migracji* Entity Framework Core, jeśli to możliwe.</span><span class="sxs-lookup"><span data-stu-id="497d3-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="497d3-102">Migracja aktualizuje schemat bazy danych, aby pasował do zmian w modelu danych.</span><span class="sxs-lookup"><span data-stu-id="497d3-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="497d3-103">Jednak migracje mogą wykonywać tylko rodzaje zmian, które obsługuje dostawca EF Core, a możliwości dostawcy oprogramowania SQLite są ograniczone.</span><span class="sxs-lookup"><span data-stu-id="497d3-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="497d3-104">Na przykład, Dodawanie kolumny jest obsługiwane, ale usunięcie lub zmiana kolumny nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="497d3-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="497d3-105">Jeśli migracja zostanie utworzona w celu usunięcia lub zmiany kolumny, `ef migrations add` polecenie zakończy się pomyślnie, ale `ef database update` polecenie zakończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="497d3-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="497d3-106">Ze względu na te ograniczenia ten samouczek nie używa migracji do zmian schematu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="497d3-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="497d3-107">Zamiast tego, gdy schemat ulegnie zmianie, należy porzucić i ponownie utworzyć bazę danych.</span><span class="sxs-lookup"><span data-stu-id="497d3-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="497d3-108">Obejście ograniczeń oprogramowania SQLite polega na ręcznym pisaniu kodu migracji w celu przetworzenia odbudowy tabeli, gdy coś w tabeli ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="497d3-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="497d3-109">Ponowne kompilowanie tabeli obejmuje:</span><span class="sxs-lookup"><span data-stu-id="497d3-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="497d3-110">Tworzenie nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="497d3-110">Creating a new table.</span></span>
>* <span data-ttu-id="497d3-111">Kopiowanie danych ze starej tabeli do nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="497d3-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="497d3-112">Porzucenie starej tabeli.</span><span class="sxs-lookup"><span data-stu-id="497d3-112">Dropping the old table.</span></span>
>* <span data-ttu-id="497d3-113">Zmiana nazwy nowej tabeli.</span><span class="sxs-lookup"><span data-stu-id="497d3-113">Renaming the new table.</span></span>
>
><span data-ttu-id="497d3-114">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="497d3-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="497d3-115">Ograniczenia dostawcy bazy danych EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="497d3-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="497d3-116">Dostosowywanie kodu migracji</span><span class="sxs-lookup"><span data-stu-id="497d3-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="497d3-117">Wstępne wypełnianie danych</span><span class="sxs-lookup"><span data-stu-id="497d3-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * [<span data-ttu-id="497d3-118">Instrukcja ALTER TABLE w programie SQLite</span><span class="sxs-lookup"><span data-stu-id="497d3-118">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)