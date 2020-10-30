---
title: ':::no-loc(Identity)::: dostosowanie modelu w ASP.NET Core'
author: ajcvickers
description: 'W tym artykule opisano sposób dostosowywania bazowego Entity Framework Core modelu danych dla programu :::no-loc(ASP.NET Core Identity)::: .'
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052048"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="68ecd-103">:::no-loc(Identity)::: dostosowanie modelu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68ecd-103">:::no-loc(Identity)::: model customization in ASP.NET Core</span></span>

<span data-ttu-id="68ecd-104">Autor [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="68ecd-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="68ecd-105">:::no-loc(ASP.NET Core Identity)::: zapewnia platformę do zarządzania kontami użytkowników i ich przechowywania w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68ecd-105">:::no-loc(ASP.NET Core Identity)::: provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="68ecd-106">:::no-loc(Identity)::: jest dodawany do projektu, gdy wybrane są **indywidualne konta użytkowników** jako mechanizm uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="68ecd-106">:::no-loc(Identity)::: is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="68ecd-107">Domyślnie program :::no-loc(Identity)::: korzysta z podstawowego modelu danych Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="68ecd-107">By default, :::no-loc(Identity)::: makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="68ecd-108">W tym artykule opisano sposób dostosowywania :::no-loc(Identity)::: modelu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-108">This article describes how to customize the :::no-loc(Identity)::: model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="68ecd-109">:::no-loc(Identity)::: i EF Core migracji</span><span class="sxs-lookup"><span data-stu-id="68ecd-109">:::no-loc(Identity)::: and EF Core Migrations</span></span>

<span data-ttu-id="68ecd-110">Przed zbadaniem modelu warto zrozumieć, jak :::no-loc(Identity)::: działa [EF Core migracji](/ef/core/managing-schemas/migrations/) w celu utworzenia i zaktualizowania bazy danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-110">Before examining the model, it's useful to understand how :::no-loc(Identity)::: works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="68ecd-111">Na najwyższego poziomu proces jest:</span><span class="sxs-lookup"><span data-stu-id="68ecd-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="68ecd-112">Zdefiniuj lub zaktualizuj [model danych w kodzie](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="68ecd-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="68ecd-113">Dodaj migrację, aby przetłumaczyć ten model na zmiany, które można zastosować do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="68ecd-114">Sprawdź, czy migracja jest poprawnie reprezentowana przez zamiary.</span><span class="sxs-lookup"><span data-stu-id="68ecd-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="68ecd-115">Zastosuj migrację, aby zaktualizować bazę danych do synchronizacji z modelem.</span><span class="sxs-lookup"><span data-stu-id="68ecd-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="68ecd-116">Powtórz kroki od 1 do 4, aby udoskonalić model i zachować synchronizację bazy danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="68ecd-117">Aby dodać i zastosować migracje, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="68ecd-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="68ecd-118">Okno **konsoli Menedżera pakietów** (PMC), jeśli jest używany program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="68ecd-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="68ecd-119">Aby uzyskać więcej informacji, zobacz [EF Core PMC Tools](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="68ecd-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="68ecd-120">Interfejs wiersza polecenia platformy .NET Core w przypadku używania wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="68ecd-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="68ecd-121">Aby uzyskać więcej informacji, zobacz [EF Core narzędzia wiersza polecenia platformy .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="68ecd-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="68ecd-122">Po uruchomieniu aplikacji kliknij przycisk **Zastosuj migracje** na stronie błędu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="68ecd-123">ASP.NET Core ma program obsługi stron błędów czasu projektowania.</span><span class="sxs-lookup"><span data-stu-id="68ecd-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="68ecd-124">Program obsługi może zastosować migracje, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="68ecd-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="68ecd-125">Aplikacje produkcyjne zwykle generują skrypty SQL z migracji i wdrażają zmiany w bazie danych w ramach kontrolowanego wdrożenia aplikacji i bazy danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="68ecd-126">Po utworzeniu nowej aplikacji :::no-loc(Identity)::: , kroki 1 i 2 powyżej zostały już ukończone.</span><span class="sxs-lookup"><span data-stu-id="68ecd-126">When a new app using :::no-loc(Identity)::: is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="68ecd-127">Oznacza to, że początkowy model danych już istnieje, a migracja początkowa została dodana do projektu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="68ecd-128">Migracja początkowa nadal musi zostać zastosowana do bazy danych programu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="68ecd-129">Migrację początkową można zastosować przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="68ecd-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="68ecd-130">Uruchamiany `Update-Database` w PMC.</span><span class="sxs-lookup"><span data-stu-id="68ecd-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="68ecd-131">Uruchom `dotnet ef database update` w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="68ecd-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="68ecd-132">Po uruchomieniu aplikacji kliknij przycisk **Zastosuj migracje** na stronie błędu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="68ecd-133">Powtórz powyższe kroki, ponieważ wprowadzono zmiany w modelu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="68ecd-134">:::no-loc(Identity):::Model</span><span class="sxs-lookup"><span data-stu-id="68ecd-134">The :::no-loc(Identity)::: model</span></span>

### <a name="entity-types"></a><span data-ttu-id="68ecd-135">Typy jednostek</span><span class="sxs-lookup"><span data-stu-id="68ecd-135">Entity types</span></span>

<span data-ttu-id="68ecd-136">:::no-loc(Identity):::Model składa się z następujących typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="68ecd-136">The :::no-loc(Identity)::: model consists of the following entity types.</span></span>

|<span data-ttu-id="68ecd-137">Typ jednostki</span><span class="sxs-lookup"><span data-stu-id="68ecd-137">Entity type</span></span>|<span data-ttu-id="68ecd-138">Opis</span><span class="sxs-lookup"><span data-stu-id="68ecd-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="68ecd-139">Reprezentuje użytkownika.</span><span class="sxs-lookup"><span data-stu-id="68ecd-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="68ecd-140">Reprezentuje rolę.</span><span class="sxs-lookup"><span data-stu-id="68ecd-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="68ecd-141">Reprezentuje stwierdzenie, że użytkownik posiada.</span><span class="sxs-lookup"><span data-stu-id="68ecd-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="68ecd-142">Reprezentuje token uwierzytelniania dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="68ecd-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="68ecd-143">Kojarzy użytkownika z logowaniem.</span><span class="sxs-lookup"><span data-stu-id="68ecd-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="68ecd-144">Reprezentuje zastrzeżenie przyznane wszystkim użytkownikom w ramach roli.</span><span class="sxs-lookup"><span data-stu-id="68ecd-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="68ecd-145">Jednostka sprzężenia, która kojarzy użytkowników i role.</span><span class="sxs-lookup"><span data-stu-id="68ecd-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="68ecd-146">Relacje typu jednostki</span><span class="sxs-lookup"><span data-stu-id="68ecd-146">Entity type relationships</span></span>

<span data-ttu-id="68ecd-147">[Typy jednostek](#entity-types) są powiązane ze sobą w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="68ecd-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="68ecd-148">Każdy z nich `User` może mieć wiele `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="68ecd-149">Każdy z nich `User` może mieć wiele `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="68ecd-150">Każdy z nich `User` może mieć wiele `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="68ecd-151">Każdy z nich `Role` może mieć wiele skojarzonych `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="68ecd-152">Każdy `User` z nich może mieć wiele skojarzonych `Roles` , a każda `Role` z nich może być skojarzona z wieloma `Users` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="68ecd-153">Jest to relacja wiele do wielu, która wymaga tabeli sprzężenia w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="68ecd-154">Tabela sprzężenia jest reprezentowana przez `UserRole` jednostkę.</span><span class="sxs-lookup"><span data-stu-id="68ecd-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="68ecd-155">Domyślna konfiguracja modelu</span><span class="sxs-lookup"><span data-stu-id="68ecd-155">Default model configuration</span></span>

<span data-ttu-id="68ecd-156">:::no-loc(Identity)::: definiuje wiele *klas kontekstu* , które dziedziczą z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) , aby skonfigurować model i korzystać z niego.</span><span class="sxs-lookup"><span data-stu-id="68ecd-156">:::no-loc(Identity)::: defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="68ecd-157">Ta konfiguracja odbywa się przy użyciu [interfejsu API EF Core Code First Fluent](/ef/core/modeling/) w metodzie [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) klasy Context.</span><span class="sxs-lookup"><span data-stu-id="68ecd-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="68ecd-158">Domyślna konfiguracja to:</span><span class="sxs-lookup"><span data-stu-id="68ecd-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="68ecd-159">Typy ogólne modelu</span><span class="sxs-lookup"><span data-stu-id="68ecd-159">Model generic types</span></span>

<span data-ttu-id="68ecd-160">:::no-loc(Identity)::: definiuje domyślne typy [środowiska uruchomieniowego języka wspólnego](/dotnet/standard/glossary#clr) (CLR) dla każdego z wymienionych powyżej typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="68ecd-160">:::no-loc(Identity)::: defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="68ecd-161">Wszystkie te typy są poprzedzone prefiksem *:::no-loc(Identity):::* :</span><span class="sxs-lookup"><span data-stu-id="68ecd-161">These types are all prefixed with *:::no-loc(Identity):::* :</span></span>

* `:::no-loc(Identity):::User`
* `:::no-loc(Identity):::Role`
* `:::no-loc(Identity):::UserClaim`
* `:::no-loc(Identity):::UserToken`
* `:::no-loc(Identity):::UserLogin`
* `:::no-loc(Identity):::RoleClaim`
* `:::no-loc(Identity):::UserRole`

<span data-ttu-id="68ecd-162">Zamiast bezpośrednio używać tych typów, typy mogą służyć jako klasy bazowe dla własnych typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="68ecd-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="68ecd-163">`DbContext`Klasy zdefiniowane przez :::no-loc(Identity)::: są ogólne, w taki sposób, aby można było używać różnych typów CLR dla co najmniej jednego typu jednostki w modelu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-163">The `DbContext` classes defined by :::no-loc(Identity)::: are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="68ecd-164">Te typy ogólne umożliwiają `User` zmianę typu danych klucza podstawowego (PK).</span><span class="sxs-lookup"><span data-stu-id="68ecd-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="68ecd-165">W przypadku używania :::no-loc(Identity)::: z obsługą ról, <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> należy użyć klasy.</span><span class="sxs-lookup"><span data-stu-id="68ecd-165">When using :::no-loc(Identity)::: with support for roles, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext> class should be used.</span></span> <span data-ttu-id="68ecd-166">Przykład:</span><span class="sxs-lookup"><span data-stu-id="68ecd-166">For example:</span></span>

```csharp
// Uses all the built-in :::no-loc(Identity)::: types
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext
    : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User, :::no-loc(Identity):::Role, string>
{
}

// Uses the built-in :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::DbContext<TUser>
    : :::no-loc(Identity):::DbContext<TUser, :::no-loc(Identity):::Role, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in :::no-loc(Identity)::: types except with custom User and Role types
// The key type is defined by TKey
public class :::no-loc(Identity):::DbContext<TUser, TRole, TKey> : :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserRole<TKey>,
    :::no-loc(Identity):::UserLogin<TKey>, :::no-loc(Identity):::RoleClaim<TKey>, :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TRole : :::no-loc(Identity):::Role<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::DbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : :::no-loc(Identity):::UserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : :::no-loc(Identity):::User<TKey>
         where TRole : :::no-loc(Identity):::Role<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
         where TUserRole : :::no-loc(Identity):::UserRole<TKey>
         where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
         where TRoleClaim : :::no-loc(Identity):::RoleClaim<TKey>
         where TUserToken : :::no-loc(Identity):::UserToken<TKey>
```

<span data-ttu-id="68ecd-167">Jest również możliwe użycie :::no-loc(Identity)::: bez ról (tylko oświadczenia), w którym przypadku <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> należy użyć klasy:</span><span class="sxs-lookup"><span data-stu-id="68ecd-167">It's also possible to use :::no-loc(Identity)::: without roles (only claims), in which case an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::UserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// Uses `string` as the key type
public class :::no-loc(Identity):::UserContext<TUser>
    : :::no-loc(Identity):::UserContext<TUser, string>
        where TUser : :::no-loc(Identity):::User
{
}

// Uses the built-in non-role :::no-loc(Identity)::: types except with a custom User type
// The key type is defined by TKey
public class :::no-loc(Identity):::UserContext<TUser, TKey> : :::no-loc(Identity):::UserContext<
    TUser, TKey, :::no-loc(Identity):::UserClaim<TKey>, :::no-loc(Identity):::UserLogin<TKey>,
    :::no-loc(Identity):::UserToken<TKey>>
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in :::no-loc(Identity)::: types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class :::no-loc(Identity):::UserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : :::no-loc(Identity):::User<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : :::no-loc(Identity):::UserClaim<TKey>
        where TUserLogin : :::no-loc(Identity):::UserLogin<TKey>
        where TUserToken : :::no-loc(Identity):::UserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="68ecd-168">Dostosowywanie modelu</span><span class="sxs-lookup"><span data-stu-id="68ecd-168">Customize the model</span></span>

<span data-ttu-id="68ecd-169">Punktem początkowym dostosowywania modelu jest wychodzenie z odpowiedniego typu kontekstu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="68ecd-170">Zapoznaj się z sekcją [typy ogólne modelu](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="68ecd-171">Ten typ kontekstu jest zazwyczaj wywoływany `ApplicationDbContext` i jest tworzony przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68ecd-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="68ecd-172">Kontekst służy do konfigurowania modelu na dwa sposoby:</span><span class="sxs-lookup"><span data-stu-id="68ecd-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="68ecd-173">Dostarczanie typów jednostek i kluczy dla parametrów typu ogólnego.</span><span class="sxs-lookup"><span data-stu-id="68ecd-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="68ecd-174">Zastępowanie `OnModelCreating` w celu zmodyfikowania mapowania tych typów.</span><span class="sxs-lookup"><span data-stu-id="68ecd-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="68ecd-175">Podczas zastępowania `OnModelCreating` `base.OnModelCreating` należy najpierw wywołać opcję przesłaniania konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="68ecd-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="68ecd-176">EF Core zwykle ma zasady dotyczące ostatniego skonfigurowania usługi WINS.</span><span class="sxs-lookup"><span data-stu-id="68ecd-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="68ecd-177">Na przykład jeśli `ToTable` Metoda dla typu jednostki jest wywoływana jako pierwsza z jedną nazwą tabeli, a następnie ponownie później z inną nazwą tabeli, używana jest nazwa tabeli w drugim wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="68ecd-178">Niestandardowe dane użytkownika</span><span class="sxs-lookup"><span data-stu-id="68ecd-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
 -->

<span data-ttu-id="68ecd-179">[Niestandardowe dane użytkownika](xref:security/authentication/add-user-data) są obsługiwane przez dziedziczenie z `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="68ecd-180">Nazwa tego typu jest niestandardowa `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="68ecd-181">Użyj `ApplicationUser` typu jako argumentu ogólnego dla kontekstu:</span><span class="sxs-lookup"><span data-stu-id="68ecd-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="68ecd-182">Nie ma potrzeby przesłonięcia `OnModelCreating` `ApplicationDbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="68ecd-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="68ecd-183">EF Core mapuje `CustomTag` Właściwość według Konwencji.</span><span class="sxs-lookup"><span data-stu-id="68ecd-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="68ecd-184">Bazę danych należy jednak zaktualizować, aby utworzyć nową `CustomTag` kolumnę.</span><span class="sxs-lookup"><span data-stu-id="68ecd-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="68ecd-185">Aby utworzyć kolumnę, Dodaj migrację, a następnie zaktualizuj bazę danych zgodnie z opisem w temacie [ :::no-loc(Identity)::: i EF Core migracji](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="68ecd-185">To create the column, add a migration, and then update the database as described in [:::no-loc(Identity)::: and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="68ecd-186">Aktualizowanie *stron/Shared/_LoginPartial. cshtml* i zastępowanie `:::no-loc(Identity):::User` `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `:::no-loc(Identity):::User` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.:::no-loc(Identity):::
@using WebApp1.Areas.:::no-loc(Identity):::.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="68ecd-187">Zaktualizuj *obszary/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* lub `Startup.ConfigureServices` Zastąp `:::no-loc(Identity):::User` ciąg `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-187">Update *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*  or `Startup.ConfigureServices` and replace `:::no-loc(Identity):::User` with `ApplicationUser`.</span></span>

```csharp
services.Add:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="68ecd-188">W ASP.NET Core 2,1 lub nowszej :::no-loc(Identity)::: jest dostarczany jako :::no-loc(Razor)::: Biblioteka klas.</span><span class="sxs-lookup"><span data-stu-id="68ecd-188">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="68ecd-189">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="68ecd-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68ecd-190">W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="68ecd-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68ecd-191">Jeśli :::no-loc(Identity)::: szkielet został użyty do dodawania :::no-loc(Identity)::: plików do projektu, Usuń wywołanie do `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-191">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="68ecd-192">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="68ecd-192">For more information, see:</span></span>

* [<span data-ttu-id="68ecd-193">Funkcja :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="68ecd-193">Scaffold :::no-loc(Identity):::</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="68ecd-194">Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do programu :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="68ecd-194">Add, download, and delete custom user data to :::no-loc(Identity):::</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="68ecd-195">Zmień typ klucza podstawowego</span><span class="sxs-lookup"><span data-stu-id="68ecd-195">Change the primary key type</span></span>

<span data-ttu-id="68ecd-196">Zmiana typu danych kolumny klucza podstawowego jest nieproblemowa w wielu systemach baz danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="68ecd-197">Zmiana klucza PK zazwyczaj obejmuje porzucenie i ponowne utworzenie tabeli.</span><span class="sxs-lookup"><span data-stu-id="68ecd-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="68ecd-198">W związku z tym typy kluczy należy określić podczas początkowej migracji podczas tworzenia bazy danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="68ecd-199">Wykonaj następujące kroki, aby zmienić typ klucza PK:</span><span class="sxs-lookup"><span data-stu-id="68ecd-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="68ecd-200">Jeśli baza danych została utworzona przed zmianą PK, uruchom polecenie `Drop-Database` (PMC) lub `dotnet ef database drop` (interfejs wiersza polecenia platformy .NET Core), aby je usunąć.</span><span class="sxs-lookup"><span data-stu-id="68ecd-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="68ecd-201">Po potwierdzeniu usunięcia bazy danych Usuń migrację początkową z `Remove-Migration` (PMC) lub `dotnet ef migrations remove` (interfejs wiersza polecenia platformy .NET Core).</span><span class="sxs-lookup"><span data-stu-id="68ecd-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="68ecd-202">Zaktualizuj `ApplicationDbContext` klasę, aby dziedziczyć z <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="68ecd-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext%603>.</span></span> <span data-ttu-id="68ecd-203">Określ nowy typ klucza `TKey` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="68ecd-204">Na przykład, aby użyć `Guid` typu klucza:</span><span class="sxs-lookup"><span data-stu-id="68ecd-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="68ecd-205">W poprzednim kodzie klasy generyczne <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> i <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> muszą być określone do użycia nowego typu klucza.</span><span class="sxs-lookup"><span data-stu-id="68ecd-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="68ecd-206">W poprzednim kodzie klasy generyczne <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> i <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> muszą być określone do użycia nowego typu klucza.</span><span class="sxs-lookup"><span data-stu-id="68ecd-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::User%601> and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::Role%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="68ecd-207">`Startup.ConfigureServices` Aby można było używać użytkownika generycznego, należy go zaktualizować:</span><span class="sxs-lookup"><span data-stu-id="68ecd-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefault:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<:::no-loc(Identity):::User<Guid>, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="68ecd-208">Jeśli `ApplicationUser` jest używana Klasa niestandardowa, zaktualizuj klasę, aby dziedziczyć `:::no-loc(Identity):::User` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `:::no-loc(Identity):::User`.</span></span> <span data-ttu-id="68ecd-209">Przykład:</span><span class="sxs-lookup"><span data-stu-id="68ecd-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="68ecd-210">Aktualizuj `ApplicationDbContext` , aby odwołać się do `ApplicationUser` klasy niestandardowej:</span><span class="sxs-lookup"><span data-stu-id="68ecd-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : :::no-loc(Identity):::DbContext<ApplicationUser, :::no-loc(Identity):::Role<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="68ecd-211">Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania :::no-loc(Identity)::: usługi w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-211">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68ecd-212">Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="68ecd-213">W ASP.NET Core 2,1 lub nowszej :::no-loc(Identity)::: jest dostarczany jako :::no-loc(Razor)::: Biblioteka klas.</span><span class="sxs-lookup"><span data-stu-id="68ecd-213">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="68ecd-214">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="68ecd-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68ecd-215">W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="68ecd-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68ecd-216">Jeśli :::no-loc(Identity)::: szkielet został użyty do dodawania :::no-loc(Identity)::: plików do projektu, Usuń wywołanie do `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-216">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68ecd-217">Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68ecd-218"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda akceptuje `TKey` Typ wskazujący typ danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="68ecd-218">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="68ecd-219">Jeśli `ApplicationRole` jest używana Klasa niestandardowa, zaktualizuj klasę, aby dziedziczyć `:::no-loc(Identity):::Role<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `:::no-loc(Identity):::Role<TKey>`.</span></span> <span data-ttu-id="68ecd-220">Przykład:</span><span class="sxs-lookup"><span data-stu-id="68ecd-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="68ecd-221">Zaktualizuj `ApplicationDbContext` , aby odwołać się do `ApplicationRole` klasy niestandardowej.</span><span class="sxs-lookup"><span data-stu-id="68ecd-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="68ecd-222">Na przykład następująca Klasa odwołuje się do niestandardowego `ApplicationUser` i niestandardowego `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68ecd-223">Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania :::no-loc(Identity)::: usługi w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-223">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="68ecd-224">Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="68ecd-225">W ASP.NET Core 2,1 lub nowszej :::no-loc(Identity)::: jest dostarczany jako :::no-loc(Razor)::: Biblioteka klas.</span><span class="sxs-lookup"><span data-stu-id="68ecd-225">In ASP.NET Core 2.1 or later, :::no-loc(Identity)::: is provided as a :::no-loc(Razor)::: Class Library.</span></span> <span data-ttu-id="68ecd-226">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="68ecd-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68ecd-227">W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="68ecd-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68ecd-228">Jeśli :::no-loc(Identity)::: szkielet został użyty do dodawania :::no-loc(Identity)::: plików do projektu, Usuń wywołanie do `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-228">If the :::no-loc(Identity)::: scaffolder was used to add :::no-loc(Identity)::: files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68ecd-229">Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania :::no-loc(Identity)::: usługi w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-229">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/:::no-loc(Razor):::PagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="68ecd-230">Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68ecd-231">Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania :::no-loc(Identity)::: usługi w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-231">Register the custom database context class when adding the :::no-loc(Identity)::: service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="68ecd-232"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda akceptuje `TKey` Typ wskazujący typ danych klucza podstawowego.</span><span class="sxs-lookup"><span data-stu-id="68ecd-232">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::EntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="68ecd-233">Dodawanie właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="68ecd-233">Add navigation properties</span></span>

<span data-ttu-id="68ecd-234">Zmiana konfiguracji modelu dla relacji może być trudniejsza niż wprowadzanie innych zmian.</span><span class="sxs-lookup"><span data-stu-id="68ecd-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="68ecd-235">Należy zwrócić uwagę, aby zastąpić istniejące relacje zamiast tworzyć nowe, dodatkowe relacje.</span><span class="sxs-lookup"><span data-stu-id="68ecd-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="68ecd-236">W szczególności zmieniona relacja musi określać tę samą właściwość klucza obcego (FK) jak istniejąca relacja.</span><span class="sxs-lookup"><span data-stu-id="68ecd-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="68ecd-237">Na przykład relacja między `Users` i `UserClaims` jest domyślnie określona w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="68ecd-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="68ecd-238">OBCY dla tej relacji jest określony jako `UserClaim.UserId` Właściwość.</span><span class="sxs-lookup"><span data-stu-id="68ecd-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="68ecd-239">`HasMany` i `WithOne` są wywoływane bez argumentów, aby utworzyć relację bez właściwości nawigacji.</span><span class="sxs-lookup"><span data-stu-id="68ecd-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="68ecd-240">Dodaj właściwość nawigacji do `ApplicationUser` , która umożliwia `UserClaims` odwołanie od użytkownika:</span><span class="sxs-lookup"><span data-stu-id="68ecd-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="68ecd-241">Wartość `TKey` dla `:::no-loc(Identity):::UserClaim<TKey>` jest typu określonego dla klucza PK dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="68ecd-241">The `TKey` for `:::no-loc(Identity):::UserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="68ecd-242">W tym przypadku `TKey` jest to `string` spowodowane tym, że są używane wartości domyślne.</span><span class="sxs-lookup"><span data-stu-id="68ecd-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="68ecd-243">To **nie** jest typ PK dla `UserClaim` typu jednostki.</span><span class="sxs-lookup"><span data-stu-id="68ecd-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="68ecd-244">Teraz, gdy istnieje właściwość nawigacji, należy ją skonfigurować w `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="68ecd-245">Należy zauważyć, że relacja jest konfigurowana dokładnie tak, jak była wcześniej, tylko z właściwością nawigacji określoną w wywołaniu `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="68ecd-246">Właściwości nawigacji istnieją tylko w modelu EF, a nie w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="68ecd-247">Ponieważ obcy dla relacji nie uległ zmianie, ten rodzaj zmiany modelu nie wymaga aktualizacji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="68ecd-248">Można to sprawdzić przez dodanie migracji po dokonaniu zmiany.</span><span class="sxs-lookup"><span data-stu-id="68ecd-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="68ecd-249">`Up`Metody i `Down` są puste.</span><span class="sxs-lookup"><span data-stu-id="68ecd-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="68ecd-250">Dodaj wszystkie właściwości nawigacji użytkownika</span><span class="sxs-lookup"><span data-stu-id="68ecd-250">Add all User navigation properties</span></span>

<span data-ttu-id="68ecd-251">Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje jednokierunkowe właściwości nawigacji dla wszystkich relacji na użytkowniku:</span><span class="sxs-lookup"><span data-stu-id="68ecd-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="68ecd-252">Dodawanie właściwości nawigacji użytkownika i roli</span><span class="sxs-lookup"><span data-stu-id="68ecd-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="68ecd-253">Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje właściwości nawigacji dla wszystkich relacji dla użytkownika i roli:</span><span class="sxs-lookup"><span data-stu-id="68ecd-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<:::no-loc(Identity):::UserClaim<string>> Claims { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserLogin<string>> Logins { get; set; }
    public virtual ICollection<:::no-loc(Identity):::UserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        :::no-loc(Identity):::UserClaim<string>, ApplicationUserRole, :::no-loc(Identity):::UserLogin<string>,
        :::no-loc(Identity):::RoleClaim<string>, :::no-loc(Identity):::UserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="68ecd-254">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="68ecd-254">Notes:</span></span>

* <span data-ttu-id="68ecd-255">Ten przykład zawiera również `UserRole` jednostkę join, która jest wymagana do nawigowania po relacji wiele-do-wielu od użytkowników do ról.</span><span class="sxs-lookup"><span data-stu-id="68ecd-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="68ecd-256">Pamiętaj, aby zmienić typy właściwości nawigacji, aby odzwierciedlić, że `Application{...}` typy są teraz używane zamiast `:::no-loc(Identity):::{...}` typów.</span><span class="sxs-lookup"><span data-stu-id="68ecd-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `:::no-loc(Identity):::{...}` types.</span></span>
* <span data-ttu-id="68ecd-257">Należy pamiętać, aby użyć `Application{...}` w definicji generycznej `ApplicationContext` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="68ecd-258">Dodaj wszystkie właściwości nawigacji</span><span class="sxs-lookup"><span data-stu-id="68ecd-258">Add all navigation properties</span></span>

<span data-ttu-id="68ecd-259">Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje właściwości nawigacji dla wszystkich relacji dla wszystkich typów jednostek:</span><span class="sxs-lookup"><span data-stu-id="68ecd-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : :::no-loc(Identity):::Role
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : :::no-loc(Identity):::UserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : :::no-loc(Identity):::UserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : :::no-loc(Identity):::UserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : :::no-loc(Identity):::RoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : :::no-loc(Identity):::UserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : :::no-loc(Identity):::DbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="68ecd-260">Użyj kluczy złożonych</span><span class="sxs-lookup"><span data-stu-id="68ecd-260">Use composite keys</span></span>

<span data-ttu-id="68ecd-261">Poprzednie sekcje przedstawiają zmianę typu klucza używanego w :::no-loc(Identity)::: modelu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-261">The preceding sections demonstrated changing the type of key used in the :::no-loc(Identity)::: model.</span></span> <span data-ttu-id="68ecd-262">Zmiana :::no-loc(Identity)::: modelu kluczy na używanie kluczy złożonych nie jest obsługiwana lub zalecana.</span><span class="sxs-lookup"><span data-stu-id="68ecd-262">Changing the :::no-loc(Identity)::: key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="68ecd-263">Użycie klucza złożonego z :::no-loc(Identity)::: wymaga zmiany sposobu, w jaki :::no-loc(Identity)::: kod Menedżera współdziała z modelem.</span><span class="sxs-lookup"><span data-stu-id="68ecd-263">Using a composite key with :::no-loc(Identity)::: involves changing how the :::no-loc(Identity)::: manager code interacts with the model.</span></span> <span data-ttu-id="68ecd-264">To dostosowanie wykracza poza zakres tego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="68ecd-265">Zmiana nazw tabel/kolumn i aspektów</span><span class="sxs-lookup"><span data-stu-id="68ecd-265">Change table/column names and facets</span></span>

<span data-ttu-id="68ecd-266">Aby zmienić nazwy tabel i kolumn, wywołaj `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="68ecd-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="68ecd-267">Następnie Dodaj konfigurację, aby zastąpić dowolne ustawienia domyślne.</span><span class="sxs-lookup"><span data-stu-id="68ecd-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="68ecd-268">Na przykład, aby zmienić nazwę wszystkich :::no-loc(Identity)::: tabel:</span><span class="sxs-lookup"><span data-stu-id="68ecd-268">For example, to change the name of all the :::no-loc(Identity)::: tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<:::no-loc(Identity):::Role>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<:::no-loc(Identity):::RoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="68ecd-269">Te przykłady używają typów domyślnych :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="68ecd-269">These examples use the default :::no-loc(Identity)::: types.</span></span> <span data-ttu-id="68ecd-270">Jeśli używasz typu aplikacji, takiego jak `ApplicationUser` , skonfiguruj ten typ zamiast typu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="68ecd-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="68ecd-271">Poniższy przykład zmienia nazwy niektórych kolumn:</span><span class="sxs-lookup"><span data-stu-id="68ecd-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="68ecd-272">Niektóre typy kolumn bazy danych można skonfigurować przy użyciu określonych *aspektów* (na przykład maksymalnej `string` dozwolonej długości).</span><span class="sxs-lookup"><span data-stu-id="68ecd-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="68ecd-273">Poniższy przykład ustawia maksymalną długość kolumny dla kilku `string` właściwości w modelu:</span><span class="sxs-lookup"><span data-stu-id="68ecd-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<:::no-loc(Identity):::User>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<:::no-loc(Identity):::UserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="68ecd-274">Mapuj na inny schemat</span><span class="sxs-lookup"><span data-stu-id="68ecd-274">Map to a different schema</span></span>

<span data-ttu-id="68ecd-275">Schematy mogą działać inaczej niż dostawcy baz danych.</span><span class="sxs-lookup"><span data-stu-id="68ecd-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="68ecd-276">W przypadku SQL Server wartością domyślną jest utworzenie wszystkich tabel w schemacie *dbo* .</span><span class="sxs-lookup"><span data-stu-id="68ecd-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="68ecd-277">Tabele można tworzyć w innym schemacie.</span><span class="sxs-lookup"><span data-stu-id="68ecd-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="68ecd-278">Przykład:</span><span class="sxs-lookup"><span data-stu-id="68ecd-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="68ecd-279">Ładowanie opóźnione</span><span class="sxs-lookup"><span data-stu-id="68ecd-279">Lazy loading</span></span>

<span data-ttu-id="68ecd-280">W tej sekcji zostanie dodana obsługa serwerów proxy ładowania opóźnionego w :::no-loc(Identity)::: modelu.</span><span class="sxs-lookup"><span data-stu-id="68ecd-280">In this section, support for lazy-loading proxies in the :::no-loc(Identity)::: model is added.</span></span> <span data-ttu-id="68ecd-281">Ładowanie z opóźnieniem jest przydatne, ponieważ umożliwia korzystanie z właściwości nawigacji bez uprzedniego załadowania.</span><span class="sxs-lookup"><span data-stu-id="68ecd-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="68ecd-282">Typy jednostek mogą być odpowiednie do ładowania z opóźnieniem na kilka sposobów, zgodnie z opisem w [dokumentacji EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="68ecd-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="68ecd-283">Dla uproszczenia Użyj serwerów proxy ładowania z opóźnieniem, które wymagają:</span><span class="sxs-lookup"><span data-stu-id="68ecd-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="68ecd-284">Instalacja pakietu [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="68ecd-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="68ecd-285">Wywołanie <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> wewnątrz [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="68ecd-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="68ecd-286">Publiczne typy jednostek z `public virtual` właściwościami nawigacji.</span><span class="sxs-lookup"><span data-stu-id="68ecd-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="68ecd-287">Poniższy przykład ilustruje wywoływanie `UseLazyLoadingProxies` w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="68ecd-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefault:::no-loc(Identity):::<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="68ecd-288">Zapoznaj się z powyższymi przykładami, aby uzyskać wskazówki dotyczące dodawania właściwości nawigacji do typów jednostek.</span><span class="sxs-lookup"><span data-stu-id="68ecd-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68ecd-289">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="68ecd-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
