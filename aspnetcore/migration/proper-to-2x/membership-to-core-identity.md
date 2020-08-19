---
title: Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0 Identity
author: isaac2004
description: Dowiedz się, jak migrować istniejące aplikacje ASP.NET przy użyciu uwierzytelniania członkostwa do ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: de9d1e5f6f595269595212fbab60d12dfd5a29e4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633646"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a><span data-ttu-id="87d1c-103">Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0 Identity</span><span class="sxs-lookup"><span data-stu-id="87d1c-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="87d1c-104">Autor [Tomasz Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="87d1c-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="87d1c-105">W tym artykule przedstawiono Migrowanie schematu bazy danych dla aplikacji ASP.NET przy użyciu uwierzytelniania członkostwa w ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="87d1c-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="87d1c-106">Ten dokument zawiera kroki niezbędne do migracji schematu bazy danych dla aplikacji opartych na członkostwie ASP.NET do schematu bazy danych używanego przez program ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="87d1c-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="87d1c-107">Aby uzyskać więcej informacji na temat migrowania z uwierzytelniania opartego na członkostwie ASP.NET do usługi ASP.NET Identity , zobacz [Migrowanie istniejącej Identity aplikacji z członkostwa SQL do usługi ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="87d1c-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="87d1c-108">Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="87d1c-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="87d1c-109">Przegląd schematu członkostwa</span><span class="sxs-lookup"><span data-stu-id="87d1c-109">Review of Membership schema</span></span>

<span data-ttu-id="87d1c-110">Przed ASP.NET 2,0, deweloperzy byli poddani do tworzenia całego procesu uwierzytelniania i autoryzacji dla swoich aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87d1c-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="87d1c-111">W przypadku ASP.NET 2,0 wprowadzono członkostwo, które zapewnia standardowe rozwiązanie do obsługi zabezpieczeń w aplikacjach ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="87d1c-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="87d1c-112">Deweloperzy mogli teraz załadować schemat do bazy danych SQL Server za pomocą polecenia [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="87d1c-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="87d1c-113">Po uruchomieniu tego polecenia następujące tabele zostały utworzone w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="87d1c-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabele członkostwa](identity/_static/membership-tables.png)

<span data-ttu-id="87d1c-115">Aby przeprowadzić migrację istniejących aplikacji do ASP.NET Core 2,0 Identity , dane w tych tabelach muszą zostać zmigrowane do tabel używanych przez nowy Identity schemat.</span><span class="sxs-lookup"><span data-stu-id="87d1c-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="no-locaspnet-core-identity-20-schema"></a><span data-ttu-id="87d1c-116">ASP.NET Core Identity Schemat 2,0</span><span class="sxs-lookup"><span data-stu-id="87d1c-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="87d1c-117">ASP.NET Core 2,0 jest zgodna z [Identity](/aspnet/identity/index) zasadą wprowadzoną w ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="87d1c-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="87d1c-118">Chociaż zasada jest współdzielona, implementacja między strukturami jest różna, nawet między wersjami ASP.NET Core (zobacz [Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="87d1c-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="87d1c-119">Najszybszym sposobem wyświetlenia schematu dla ASP.NET Core 2,0 Identity jest utworzenie nowej aplikacji ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="87d1c-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="87d1c-120">Wykonaj następujące kroki w programie Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="87d1c-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="87d1c-121">Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="87d1c-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="87d1c-122">Utwórz nowy projekt **aplikacji sieci Web ASP.NET Core** o nazwie *Core Identity Sample*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="87d1c-123">Wybierz pozycję **ASP.NET Core 2,0** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="87d1c-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="87d1c-124">Ten szablon generuje aplikację [ Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="87d1c-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="87d1c-125">Przed kliknięciem przycisku **OK**kliknij pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="87d1c-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="87d1c-126">Wybierz **konta poszczególnych użytkowników** dla Identity szablonów.</span><span class="sxs-lookup"><span data-stu-id="87d1c-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="87d1c-127">Na koniec kliknij przycisk **OK**, a następnie **OK**.</span><span class="sxs-lookup"><span data-stu-id="87d1c-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="87d1c-128">Program Visual Studio tworzy projekt przy użyciu ASP.NET Core Identity szablonu.</span><span class="sxs-lookup"><span data-stu-id="87d1c-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="87d1c-129">Wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera** pakietów, aby otworzyć okno **konsoli Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="87d1c-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="87d1c-130">Przejdź do katalogu głównego projektu w PMC i uruchom polecenie [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="87d1c-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="87d1c-131">ASP.NET Core 2,0 Identity używa EF Core do korzystania z bazy danych przechowującej dane uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="87d1c-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="87d1c-132">Aby nowo utworzona aplikacja działała, musi być bazą danych do przechowywania tych danych.</span><span class="sxs-lookup"><span data-stu-id="87d1c-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="87d1c-133">Po utworzeniu nowej aplikacji najszybszym sposobem na sprawdzenie schematu w środowisku bazy danych jest utworzenie bazy danych przy użyciu [EF Core migracji](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="87d1c-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="87d1c-134">Ten proces powoduje utworzenie bazy danych lokalnie lub w innym miejscu, która śladuje ten schemat.</span><span class="sxs-lookup"><span data-stu-id="87d1c-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="87d1c-135">Zapoznaj się z powyższą dokumentacją, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="87d1c-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="87d1c-136">Polecenia EF Core używają parametrów połączenia dla bazy danych określonej w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="87d1c-137">Następujące parametry połączenia są przeznaczone dla bazy danych na *hoście lokalnym* o nazwie *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="87d1c-138">W tym ustawieniu EF Core jest skonfigurowany do używania `DefaultConnection` parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="87d1c-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="87d1c-139">Wybierz pozycję **Wyświetl**  >  **Eksplorator obiektów SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="87d1c-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="87d1c-140">Rozwiń węzeł odpowiadający nazwie bazy danych określonej we `ConnectionStrings:DefaultConnection` właściwości *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="87d1c-141">`Update-Database`Polecenie utworzyło bazę danych określoną za pomocą schematu i wszystkie dane potrzebne do zainicjowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="87d1c-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="87d1c-142">Na poniższej ilustracji przedstawiono strukturę tabeli, która została utworzona z poprzednimi krokami.</span><span class="sxs-lookup"><span data-stu-id="87d1c-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![::: No-Loc (tożsamość)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="87d1c-144">Migrowanie schematu</span><span class="sxs-lookup"><span data-stu-id="87d1c-144">Migrate the schema</span></span>

<span data-ttu-id="87d1c-145">Istnieją delikatne różnice w strukturach tabel i polach dla członkostwa i ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="87d1c-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="87d1c-146">Wzorzec został znacząco zmieniony na potrzeby uwierzytelniania/autoryzacji za pomocą aplikacji ASP.NET i ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87d1c-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="87d1c-147">Obiekty kluczowe, które są nadal używane w połączeniu z Identity *użytkownikami* i *rolami*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="87d1c-148">Oto Mapowanie tabel dla *użytkowników*, *ról*i *roli użytkownika*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="87d1c-149">Użytkownicy</span><span class="sxs-lookup"><span data-stu-id="87d1c-149">Users</span></span>

|Identity<br><span data-ttu-id="87d1c-150">( `dbo.AspNetUsers` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-150">(`dbo.AspNetUsers`) column</span></span>  |<span data-ttu-id="87d1c-151">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-151">Type</span></span>     |<span data-ttu-id="87d1c-152">Członkostwo</span><span class="sxs-lookup"><span data-stu-id="87d1c-152">Membership</span></span><br><span data-ttu-id="87d1c-153">( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-153">(`dbo.aspnet_Users` / `dbo.aspnet_Membership`) column</span></span>|<span data-ttu-id="87d1c-154">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-154">Type</span></span>      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> <span data-ttu-id="87d1c-155">Nie wszystkie mapowania pól są podobne do relacji jeden-do-jednego z członkostwa do ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="87d1c-155">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="87d1c-156">Powyższa tabela przyjmuje domyślny schemat użytkownika członkostwa i mapuje go na ASP.NET Core Identity schemat.</span><span class="sxs-lookup"><span data-stu-id="87d1c-156">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="87d1c-157">Wszystkie inne pola niestandardowe, które były używane na potrzeby członkostwa, muszą być mapowane ręcznie.</span><span class="sxs-lookup"><span data-stu-id="87d1c-157">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="87d1c-158">W przypadku tego mapowania nie ma mapy haseł, ponieważ zarówno kryterium hasła, jak i sole hasła nie są migrowane między nimi.</span><span class="sxs-lookup"><span data-stu-id="87d1c-158">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="87d1c-159">**Zaleca się pozostawienie hasła jako wartości null i poproszenie użytkowników o zresetowanie haseł.**</span><span class="sxs-lookup"><span data-stu-id="87d1c-159">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="87d1c-160">W ASP.NET Core Identity programie `LockoutEnd` należy ustawić na pewną datę w przyszłości, jeśli użytkownik jest zablokowany. Jest to pokazane w skrypcie migracji.</span><span class="sxs-lookup"><span data-stu-id="87d1c-160">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="87d1c-161">Role</span><span class="sxs-lookup"><span data-stu-id="87d1c-161">Roles</span></span>

|Identity<br><span data-ttu-id="87d1c-162">( `dbo.AspNetRoles` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-162">(`dbo.AspNetRoles`) column</span></span>|<span data-ttu-id="87d1c-163">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-163">Type</span></span>|<span data-ttu-id="87d1c-164">Członkostwo</span><span class="sxs-lookup"><span data-stu-id="87d1c-164">Membership</span></span><br><span data-ttu-id="87d1c-165">( `dbo.aspnet_Roles` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-165">(`dbo.aspnet_Roles`) column</span></span>|<span data-ttu-id="87d1c-166">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-166">Type</span></span>|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a><span data-ttu-id="87d1c-167">Role użytkowników</span><span class="sxs-lookup"><span data-stu-id="87d1c-167">User Roles</span></span>

|Identity<br><span data-ttu-id="87d1c-168">( `dbo.AspNetUserRoles` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-168">(`dbo.AspNetUserRoles`) column</span></span>|<span data-ttu-id="87d1c-169">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-169">Type</span></span>|<span data-ttu-id="87d1c-170">Członkostwo</span><span class="sxs-lookup"><span data-stu-id="87d1c-170">Membership</span></span><br><span data-ttu-id="87d1c-171">( `dbo.aspnet_UsersInRoles` ) kolumna</span><span class="sxs-lookup"><span data-stu-id="87d1c-171">(`dbo.aspnet_UsersInRoles`) column</span></span>|<span data-ttu-id="87d1c-172">Typ</span><span class="sxs-lookup"><span data-stu-id="87d1c-172">Type</span></span>|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

<span data-ttu-id="87d1c-173">Odwołuje się do powyższej tabeli mapowania podczas tworzenia skryptu migracji dla *użytkowników* i *ról*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-173">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="87d1c-174">W poniższym przykładzie założono, że masz dwie bazy danych na serwerze bazy danych.</span><span class="sxs-lookup"><span data-stu-id="87d1c-174">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="87d1c-175">Jedna baza danych zawiera istniejący schemat i dane członkostwa ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="87d1c-175">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="87d1c-176">Druga *podstawowa Identity Przykładowa* baza danych została utworzona przy użyciu opisanej wcześniej procedury.</span><span class="sxs-lookup"><span data-stu-id="87d1c-176">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="87d1c-177">Komentarze są zawarte w tekście, aby uzyskać więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="87d1c-177">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="87d1c-178">Po zakończeniu poprzedniego skryptu ASP.NET Core Identity utworzona wcześniej aplikacja zostanie wypełniona użytkownikami członkostwa.</span><span class="sxs-lookup"><span data-stu-id="87d1c-178">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="87d1c-179">Użytkownicy muszą zmienić swoje hasła przed zalogowaniem się.</span><span class="sxs-lookup"><span data-stu-id="87d1c-179">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="87d1c-180">Jeśli system członkostwa ma użytkowników z nazwami użytkowników, którzy nie są zgodni z ich adresem e-mail, zmiany są wymagane w przypadku aplikacji utworzonej wcześniej w celu tego celu.</span><span class="sxs-lookup"><span data-stu-id="87d1c-180">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="87d1c-181">Szablon domyślny oczekuje `UserName` i jest taki `Email` sam.</span><span class="sxs-lookup"><span data-stu-id="87d1c-181">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="87d1c-182">W sytuacjach, w których są różne, proces logowania należy zmodyfikować do użycia `UserName` zamiast `Email` .</span><span class="sxs-lookup"><span data-stu-id="87d1c-182">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="87d1c-183">Na `PageModel` stronie logowania, która znajduje się w *Pages\Account\Login.cshtml.cs*, Usuń `[EmailAddress]` atrybut z właściwości *email* .</span><span class="sxs-lookup"><span data-stu-id="87d1c-183">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="87d1c-184">Zmień nazwę na nazwę *użytkownika*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-184">Rename it to *UserName*.</span></span> <span data-ttu-id="87d1c-185">Wymaga to zmiany wszędzie tam `EmailAddress` , gdzie jest wymieniony, w *widoku* i *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="87d1c-185">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="87d1c-186">Wynik będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="87d1c-186">The result looks like the following:</span></span>

 ![Stałe logowanie](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="87d1c-188">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="87d1c-188">Next steps</span></span>

<span data-ttu-id="87d1c-189">W tym samouczku pokazano, jak przenieść użytkowników z członkostwa SQL do ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="87d1c-189">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="87d1c-190">Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="87d1c-190">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
