---
title: Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0Identity
author: isaac2004
description: Dowiedz się, jak migrować istniejące aplikacje ASP.NET przy użyciu uwierzytelniania członkostwa do ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: f039772f4276d0e8bcec2629350eba2ec0e7418c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399689"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="56b24-103">Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0Identity</span><span class="sxs-lookup"><span data-stu-id="56b24-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="56b24-104">Autor [Tomasz Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="56b24-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="56b24-105">W tym artykule przedstawiono Migrowanie schematu bazy danych dla aplikacji ASP.NET przy użyciu uwierzytelniania członkostwa w ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="56b24-106">Ten dokument zawiera kroki niezbędne do przeprowadzenia migracji schematu bazy danych dla aplikacji opartych na członkostwie ASP.NET do schematu bazy danych używanego dla ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="56b24-107">Aby uzyskać więcej informacji na temat migrowania z uwierzytelniania opartego na członkostwie ASP.NET do usługi ASP.NET Identity , zobacz [Migrowanie istniejącej Identity aplikacji z członkostwa SQL do usługi ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="56b24-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="56b24-108">Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="56b24-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="56b24-109">Przegląd schematu członkostwa</span><span class="sxs-lookup"><span data-stu-id="56b24-109">Review of Membership schema</span></span>

<span data-ttu-id="56b24-110">Przed ASP.NET 2,0, deweloperzy byli poddani do tworzenia całego procesu uwierzytelniania i autoryzacji dla swoich aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56b24-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="56b24-111">W przypadku ASP.NET 2,0 wprowadzono członkostwo, które zapewnia standardowe rozwiązanie do obsługi zabezpieczeń w aplikacjach ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="56b24-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="56b24-112">Deweloperzy mogli teraz załadować schemat do bazy danych SQL Server za pomocą polecenia [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="56b24-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="56b24-113">Po uruchomieniu tego polecenia następujące tabele zostały utworzone w bazie danych programu.</span><span class="sxs-lookup"><span data-stu-id="56b24-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabele członkostwa](identity/_static/membership-tables.png)

<span data-ttu-id="56b24-115">Aby przeprowadzić migrację istniejących aplikacji do ASP.NET Core 2,0 Identity , dane w tych tabelach muszą zostać zmigrowane do tabel używanych przez nowy Identity schemat.</span><span class="sxs-lookup"><span data-stu-id="56b24-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="56b24-116">IdentitySchemat ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="56b24-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="56b24-117">ASP.NET Core 2,0 jest zgodna z [Identity](/aspnet/identity/index) zasadą wprowadzoną w ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="56b24-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="56b24-118">Chociaż zasada jest współdzielona, implementacja między strukturami jest różna, nawet między wersjami ASP.NET Core (zobacz [Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="56b24-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="56b24-119">Najszybszym sposobem wyświetlenia schematu dla ASP.NET Core 2,0 Identity jest utworzenie nowej aplikacji ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="56b24-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="56b24-120">Wykonaj następujące kroki w programie Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="56b24-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="56b24-121">Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.</span><span class="sxs-lookup"><span data-stu-id="56b24-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="56b24-122">Utwórz nowy projekt **ASP.NET Core aplikacji sieci Web** o nazwie *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="56b24-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="56b24-123">Wybierz pozycję **ASP.NET Core 2,0** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="56b24-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="56b24-124">Ten szablon generuje aplikację [ Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="56b24-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="56b24-125">Przed kliknięciem przycisku **OK**kliknij pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="56b24-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="56b24-126">Wybierz **konta poszczególnych użytkowników** dla Identity szablonów.</span><span class="sxs-lookup"><span data-stu-id="56b24-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="56b24-127">Na koniec kliknij przycisk **OK**, a następnie **OK**.</span><span class="sxs-lookup"><span data-stu-id="56b24-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="56b24-128">Program Visual Studio tworzy projekt przy użyciu Identity szablonu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56b24-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="56b24-129">Wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera** pakietów, aby otworzyć okno **konsoli Menedżera pakietów** (PMC).</span><span class="sxs-lookup"><span data-stu-id="56b24-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="56b24-130">Przejdź do katalogu głównego projektu w PMC i uruchom polecenie [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="56b24-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="56b24-131">ASP.NET Core 2,0 Identity używa EF Core do korzystania z bazy danych przechowującej dane uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="56b24-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="56b24-132">Aby nowo utworzona aplikacja działała, musi być bazą danych do przechowywania tych danych.</span><span class="sxs-lookup"><span data-stu-id="56b24-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="56b24-133">Po utworzeniu nowej aplikacji najszybszym sposobem na sprawdzenie schematu w środowisku bazy danych jest utworzenie bazy danych przy użyciu [EF Core migracji](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="56b24-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="56b24-134">Ten proces powoduje utworzenie bazy danych lokalnie lub w innym miejscu, która śladuje ten schemat.</span><span class="sxs-lookup"><span data-stu-id="56b24-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="56b24-135">Zapoznaj się z powyższą dokumentacją, aby uzyskać więcej informacji.</span><span class="sxs-lookup"><span data-stu-id="56b24-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="56b24-136">Polecenia EF Core używają parametrów połączenia dla bazy danych określonej w *appsettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="56b24-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="56b24-137">Następujące parametry połączenia są przeznaczone dla bazy danych na *hoście lokalnym* o nazwie *ASP-NET-Core-Identity*.</span><span class="sxs-lookup"><span data-stu-id="56b24-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="56b24-138">W tym ustawieniu EF Core jest skonfigurowany do używania `DefaultConnection` parametrów połączenia.</span><span class="sxs-lookup"><span data-stu-id="56b24-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="56b24-139">Wybierz pozycję **Wyświetl**  >  **Eksplorator obiektów SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="56b24-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="56b24-140">Rozwiń węzeł odpowiadający nazwie bazy danych określonej we `ConnectionStrings:DefaultConnection` właściwości *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="56b24-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="56b24-141">`Update-Database`Polecenie utworzyło bazę danych określoną za pomocą schematu i wszystkie dane potrzebne do zainicjowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56b24-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="56b24-142">Na poniższej ilustracji przedstawiono strukturę tabeli, która została utworzona z poprzednimi krokami.</span><span class="sxs-lookup"><span data-stu-id="56b24-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    <span data-ttu-id="56b24-143">![IdentityTabelę](identity/_static/identity-tables.png)</span><span class="sxs-lookup"><span data-stu-id="56b24-143">![Identity Tables](identity/_static/identity-tables.png)</span></span>

## <a name="migrate-the-schema"></a><span data-ttu-id="56b24-144">Migrowanie schematu</span><span class="sxs-lookup"><span data-stu-id="56b24-144">Migrate the schema</span></span>

<span data-ttu-id="56b24-145">Istnieją delikatne różnice w strukturach tabel i polach zarówno dla członkostwa, jak i ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="56b24-146">Wzorzec został znacząco zmieniony na potrzeby uwierzytelniania/autoryzacji za pomocą aplikacji ASP.NET i ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56b24-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="56b24-147">Obiekty kluczowe, które są nadal używane w połączeniu z Identity *użytkownikami* i *rolami*.</span><span class="sxs-lookup"><span data-stu-id="56b24-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="56b24-148">Oto Mapowanie tabel dla *użytkowników*, *ról*i *roli użytkownika*.</span><span class="sxs-lookup"><span data-stu-id="56b24-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="56b24-149">Użytkownicy</span><span class="sxs-lookup"><span data-stu-id="56b24-149">Users</span></span>

|<span data-ttu-id="56b24-150">*Identity<br>dbo. AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="56b24-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="56b24-151">*Członkostwo <br> (dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="56b24-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="56b24-152">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-152">**Field Name**</span></span>                 |<span data-ttu-id="56b24-153">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-153">**Type**</span></span>|<span data-ttu-id="56b24-154">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-154">**Field Name**</span></span>                                    |<span data-ttu-id="56b24-155">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="56b24-156">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="56b24-157">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="56b24-158">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="56b24-159">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="56b24-160">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="56b24-161">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="56b24-162">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="56b24-163">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="56b24-164">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="56b24-165">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="56b24-166">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="56b24-167">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="56b24-168">bit</span><span class="sxs-lookup"><span data-stu-id="56b24-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="56b24-169">bit</span><span class="sxs-lookup"><span data-stu-id="56b24-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="56b24-170">Nie wszystkie mapowania pól są podobne do relacji jeden-do-jednego z członkostwa do ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="56b24-171">Powyższa tabela przyjmuje domyślny schemat użytkownika członkostwa i mapuje go na schemat ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="56b24-172">Wszystkie inne pola niestandardowe, które były używane na potrzeby członkostwa, muszą być mapowane ręcznie.</span><span class="sxs-lookup"><span data-stu-id="56b24-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="56b24-173">W przypadku tego mapowania nie ma mapy haseł, ponieważ zarówno kryterium hasła, jak i sole hasła nie są migrowane między nimi.</span><span class="sxs-lookup"><span data-stu-id="56b24-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="56b24-174">**Zaleca się pozostawienie hasła jako wartości null i poproszenie użytkowników o zresetowanie haseł.**</span><span class="sxs-lookup"><span data-stu-id="56b24-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="56b24-175">W ASP.NET Core Identity , `LockoutEnd` należy ustawić na pewną datę w przyszłości, jeśli użytkownik jest zablokowany. Jest to pokazane w skrypcie migracji.</span><span class="sxs-lookup"><span data-stu-id="56b24-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="56b24-176">Role</span><span class="sxs-lookup"><span data-stu-id="56b24-176">Roles</span></span>

|<span data-ttu-id="56b24-177">*Identity<br>dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="56b24-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="56b24-178">*Członkostwo <br> (dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="56b24-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="56b24-179">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-179">**Field Name**</span></span>                 |<span data-ttu-id="56b24-180">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-180">**Type**</span></span>|<span data-ttu-id="56b24-181">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-181">**Field Name**</span></span>   |<span data-ttu-id="56b24-182">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="56b24-183">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-183">string</span></span>  |`RoleId`         | <span data-ttu-id="56b24-184">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="56b24-185">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-185">string</span></span>  |`RoleName`       | <span data-ttu-id="56b24-186">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="56b24-187">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="56b24-188">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="56b24-189">Role użytkowników</span><span class="sxs-lookup"><span data-stu-id="56b24-189">User Roles</span></span>

|<span data-ttu-id="56b24-190">*Identity<br>dbo. AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="56b24-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="56b24-191">*Członkostwo <br> (dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="56b24-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="56b24-192">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-192">**Field Name**</span></span>           |<span data-ttu-id="56b24-193">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-193">**Type**</span></span>  |<span data-ttu-id="56b24-194">**Nazwa pola**</span><span class="sxs-lookup"><span data-stu-id="56b24-194">**Field Name**</span></span>|<span data-ttu-id="56b24-195">**Typ**</span><span class="sxs-lookup"><span data-stu-id="56b24-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="56b24-196">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-196">string</span></span>    |`RoleId`      |<span data-ttu-id="56b24-197">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="56b24-198">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-198">string</span></span>    |`UserId`      |<span data-ttu-id="56b24-199">ciąg</span><span class="sxs-lookup"><span data-stu-id="56b24-199">string</span></span>                     |

<span data-ttu-id="56b24-200">Odwołuje się do powyższej tabeli mapowania podczas tworzenia skryptu migracji dla *użytkowników* i *ról*.</span><span class="sxs-lookup"><span data-stu-id="56b24-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="56b24-201">W poniższym przykładzie założono, że masz dwie bazy danych na serwerze bazy danych.</span><span class="sxs-lookup"><span data-stu-id="56b24-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="56b24-202">Jedna baza danych zawiera istniejący schemat i dane członkostwa ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="56b24-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="56b24-203">Druga baza danych *CoreIdentitySample* została utworzona za pomocą opisanej wcześniej procedury.</span><span class="sxs-lookup"><span data-stu-id="56b24-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="56b24-204">Komentarze są zawarte w tekście, aby uzyskać więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="56b24-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="56b24-205">Po ukończeniu poprzedniego skryptu Identity utworzona wcześniej aplikacja ASP.NET Core zostanie wypełniona użytkownikom członkostwa.</span><span class="sxs-lookup"><span data-stu-id="56b24-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="56b24-206">Użytkownicy muszą zmienić swoje hasła przed zalogowaniem się.</span><span class="sxs-lookup"><span data-stu-id="56b24-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="56b24-207">Jeśli system członkostwa ma użytkowników z nazwami użytkowników, którzy nie są zgodni z ich adresem e-mail, zmiany są wymagane w przypadku aplikacji utworzonej wcześniej w celu tego celu.</span><span class="sxs-lookup"><span data-stu-id="56b24-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="56b24-208">Szablon domyślny oczekuje `UserName` i jest taki `Email` sam.</span><span class="sxs-lookup"><span data-stu-id="56b24-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="56b24-209">W sytuacjach, w których są różne, proces logowania należy zmodyfikować do użycia `UserName` zamiast `Email` .</span><span class="sxs-lookup"><span data-stu-id="56b24-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="56b24-210">Na `PageModel` stronie logowania, która znajduje się w *Pages\Account\Login.cshtml.cs*, Usuń `[EmailAddress]` atrybut z właściwości *email* .</span><span class="sxs-lookup"><span data-stu-id="56b24-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="56b24-211">Zmień nazwę na nazwę *użytkownika*.</span><span class="sxs-lookup"><span data-stu-id="56b24-211">Rename it to *UserName*.</span></span> <span data-ttu-id="56b24-212">Wymaga to zmiany wszędzie tam `EmailAddress` , gdzie jest wymieniony, w *widoku* i *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="56b24-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="56b24-213">Wynik będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="56b24-213">The result looks like the following:</span></span>

 ![Stałe logowanie](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="56b24-215">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="56b24-215">Next steps</span></span>

<span data-ttu-id="56b24-216">W tym samouczku pokazano, jak przenieść użytkowników z członkostwa SQL do ASP.NET Core 2,0 Identity .</span><span class="sxs-lookup"><span data-stu-id="56b24-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="56b24-217">Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="56b24-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
