---
title: Identitydostosowanie modelu w ASP.NET Core
author: ajcvickers
description: W tym artykule opisano sposób dostosowywania bazowego modelu danych Entity Framework Core dla ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 96ee703da4ced69c5d9c703139e33b76b5dcdff1
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074146"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Identitydostosowanie modelu w ASP.NET Core

Autor [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity zapewnia platformę do zarządzania kontami użytkowników w aplikacjach ASP.NET Core i ich przechowywania. Identityjest dodawany do projektu, gdy wybrane są **indywidualne konta użytkowników** jako mechanizm uwierzytelniania. Domyślnie program Identity korzysta z podstawowego modelu danych Entity Framework (EF). W tym artykule opisano sposób dostosowywania Identity modelu.

## <a name="identity-and-ef-core-migrations"></a>Identityi EF Core migracji

Przed zbadaniem modelu warto zrozumieć, jak Identity działa [EF Core migracji](/ef/core/managing-schemas/migrations/) w celu utworzenia i zaktualizowania bazy danych. Na najwyższego poziomu proces jest:

1. Zdefiniuj lub zaktualizuj [model danych w kodzie](/ef/core/modeling/).
1. Dodaj migrację, aby przetłumaczyć ten model na zmiany, które można zastosować do bazy danych programu.
1. Sprawdź, czy migracja jest poprawnie reprezentowana przez zamiary.
1. Zastosuj migrację, aby zaktualizować bazę danych do synchronizacji z modelem.
1. Powtórz kroki od 1 do 4, aby udoskonalić model i zachować synchronizację bazy danych.

Aby dodać i zastosować migracje, należy użyć jednej z następujących metod:

* Okno **konsoli Menedżera pakietów** (PMC), jeśli jest używany program Visual Studio. Aby uzyskać więcej informacji, zobacz [EF Core PMC Tools](/ef/core/miscellaneous/cli/powershell).
* Interfejs wiersza polecenia platformy .NET Core w przypadku używania wiersza polecenia. Aby uzyskać więcej informacji, zobacz [EF Core narzędzia wiersza polecenia platformy .NET](/ef/core/miscellaneous/cli/dotnet).
* Po uruchomieniu aplikacji kliknij przycisk **Zastosuj migracje** na stronie błędu.

ASP.NET Core ma program obsługi stron błędów czasu projektowania. Program obsługi może zastosować migracje, gdy aplikacja jest uruchomiona. Aplikacje produkcyjne zwykle generują skrypty SQL z migracji i wdrażają zmiany w bazie danych w ramach kontrolowanego wdrożenia aplikacji i bazy danych.

Po utworzeniu nowej aplikacji Identity , kroki 1 i 2 powyżej zostały już ukończone. Oznacza to, że początkowy model danych już istnieje, a migracja początkowa została dodana do projektu. Migracja początkowa nadal musi zostać zastosowana do bazy danych programu. Migrację początkową można zastosować przy użyciu jednej z następujących metod:

* Uruchamiany `Update-Database` w PMC.
* Uruchom `dotnet ef database update` w powłoce poleceń.
* Po uruchomieniu aplikacji kliknij przycisk **Zastosuj migracje** na stronie błędu.

Powtórz powyższe kroki, ponieważ wprowadzono zmiany w modelu.

## <a name="the-identity-model"></a>IdentityModel

### <a name="entity-types"></a>Typy jednostek

IdentityModel składa się z następujących typów jednostek.

|Typ jednostki|Opis                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Reprezentuje użytkownika.                                         |
|`Role`     |Reprezentuje rolę.                                           |
|`UserClaim`|Reprezentuje stwierdzenie, że użytkownik posiada.                    |
|`UserToken`|Reprezentuje token uwierzytelniania dla użytkownika.               |
|`UserLogin`|Kojarzy użytkownika z logowaniem.                              |
|`RoleClaim`|Reprezentuje zastrzeżenie przyznane wszystkim użytkownikom w ramach roli.|
|`UserRole` |Jednostka sprzężenia, która kojarzy użytkowników i role.               |

### <a name="entity-type-relationships"></a>Relacje typu jednostki

[Typy jednostek](#entity-types) są powiązane ze sobą w następujący sposób:

* Każdy z nich `User` może mieć wiele `UserClaims` .
* Każdy z nich `User` może mieć wiele `UserLogins` .
* Każdy z nich `User` może mieć wiele `UserTokens` .
* Każdy z nich `Role` może mieć wiele skojarzonych `RoleClaims` .
* Każdy `User` z nich może mieć wiele skojarzonych `Roles` , a każda `Role` z nich może być skojarzona z wieloma `Users` . Jest to relacja wiele do wielu, która wymaga tabeli sprzężenia w bazie danych. Tabela sprzężenia jest reprezentowana przez `UserRole` jednostkę.

### <a name="default-model-configuration"></a>Domyślna konfiguracja modelu

Identitydefiniuje wiele *klas kontekstu* , które dziedziczą z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) , aby skonfigurować model i korzystać z niego. Ta konfiguracja odbywa się przy użyciu [interfejsu API EF Core Code First Fluent](/ef/core/modeling/) w metodzie [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) klasy Context. Domyślna konfiguracja to:

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

### <a name="model-generic-types"></a>Typy ogólne modelu

Identitydefiniuje domyślne typy [środowiska uruchomieniowego języka wspólnego](/dotnet/standard/glossary#clr) (CLR) dla każdego z wymienionych powyżej typów jednostek. Wszystkie te typy są poprzedzone prefiksem *Identity* :

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Zamiast bezpośrednio używać tych typów, typy mogą służyć jako klasy bazowe dla własnych typów aplikacji. `DbContext`Klasy zdefiniowane przez Identity są ogólne, w taki sposób, aby można było używać różnych typów CLR dla co najmniej jednego typu jednostki w modelu. Te typy ogólne umożliwiają `User` zmianę typu danych klucza podstawowego (PK).

W przypadku używania Identity z obsługą ról, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> należy użyć klasy. Przykład:

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Jest również możliwe użycie Identity bez ról (tylko oświadczenia), w którym przypadku <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> należy użyć klasy:

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>Dostosowywanie modelu

Punktem początkowym dostosowywania modelu jest wychodzenie z odpowiedniego typu kontekstu. Zapoznaj się z sekcją [typy ogólne modelu](#model-generic-types) . Ten typ kontekstu jest zazwyczaj wywoływany `ApplicationDbContext` i jest tworzony przez szablony ASP.NET Core.

Kontekst służy do konfigurowania modelu na dwa sposoby:

* Dostarczanie typów jednostek i kluczy dla parametrów typu ogólnego.
* Zastępowanie `OnModelCreating` w celu zmodyfikowania mapowania tych typów.

Podczas zastępowania `OnModelCreating` `base.OnModelCreating` należy najpierw wywołać opcję przesłaniania konfiguracji. EF Core zwykle ma zasady dotyczące ostatniego skonfigurowania usługi WINS. Na przykład jeśli `ToTable` Metoda dla typu jednostki jest wywoływana jako pierwsza z jedną nazwą tabeli, a następnie ponownie później z inną nazwą tabeli, używana jest nazwa tabeli w drugim wywołaniu.

### <a name="custom-user-data"></a>Niestandardowe dane użytkownika

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Niestandardowe dane użytkownika](xref:security/authentication/add-user-data) są obsługiwane przez dziedziczenie z `IdentityUser` . Nazwa tego typu jest niestandardowa `ApplicationUser` :

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Użyj `ApplicationUser` typu jako argumentu ogólnego dla kontekstu:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

Nie ma potrzeby przesłonięcia `OnModelCreating` `ApplicationDbContext` klasy. EF Core mapuje `CustomTag` Właściwość według Konwencji. Bazę danych należy jednak zaktualizować, aby utworzyć nową `CustomTag` kolumnę. Aby utworzyć kolumnę, Dodaj migrację, a następnie zaktualizuj bazę danych zgodnie z opisem w temacie [ Identity i EF Core migracji](#identity-and-ef-core-migrations).

Aktualizowanie *stron/Shared/_LoginPartial. cshtml* i zastępowanie `IdentityUser` `ApplicationUser` :

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Zaktualizuj *obszary/ Identity /IdentityHostingStartup.cs* lub `Startup.ConfigureServices` Zastąp `IdentityUser` ciąg `ApplicationUser` .

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

W ASP.NET Core 2,1 lub nowszej Identity jest dostarczany jako Razor Biblioteka klas. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>. W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Jeśli Identity szkielet został użyty do dodawania Identity plików do projektu, Usuń wywołanie do `AddDefaultUI` . Aby uzyskać więcej informacji, zobacz:

* [FunkcjaIdentity](xref:security/authentication/scaffold-identity)
* [Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do programuIdentity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Zmień typ klucza podstawowego

Zmiana typu danych kolumny klucza podstawowego jest nieproblemowa w wielu systemach baz danych. Zmiana klucza PK zazwyczaj obejmuje porzucenie i ponowne utworzenie tabeli. W związku z tym typy kluczy należy określić podczas początkowej migracji podczas tworzenia bazy danych.

Wykonaj następujące kroki, aby zmienić typ klucza PK:

1. Jeśli baza danych została utworzona przed zmianą PK, uruchom polecenie `Drop-Database` (PMC) lub `dotnet ef database drop` (interfejs wiersza polecenia platformy .NET Core), aby je usunąć.
2. Po potwierdzeniu usunięcia bazy danych Usuń migrację początkową z `Remove-Migration` (PMC) lub `dotnet ef migrations remove` (interfejs wiersza polecenia platformy .NET Core).
3. Zaktualizuj `ApplicationDbContext` klasę, aby dziedziczyć z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> . Określ nowy typ klucza `TKey` . Na przykład, aby użyć `Guid` typu klucza:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    W poprzednim kodzie klasy generyczne <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> i <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> muszą być określone do użycia nowego typu klucza.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    W poprzednim kodzie klasy generyczne <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> i <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> muszą być określone do użycia nowego typu klucza.

    ::: moniker-end

    `Startup.ConfigureServices`Aby można było używać użytkownika generycznego, należy go zaktualizować:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. Jeśli `ApplicationUser` jest używana Klasa niestandardowa, zaktualizuj klasę, aby dziedziczyć `IdentityUser` . Przykład:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Aktualizuj `ApplicationDbContext` , aby odwołać się do `ApplicationUser` klasy niestandardowej:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania Identity usługi w programie `Startup.ConfigureServices` :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    W ASP.NET Core 2,1 lub nowszej Identity jest dostarczany jako Razor Biblioteka klas. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>. W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Jeśli Identity szkielet został użyty do dodawania Identity plików do projektu, Usuń wywołanie do `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda akceptuje `TKey` Typ wskazujący typ danych klucza podstawowego.

    ::: moniker-end

5. Jeśli `ApplicationRole` jest używana Klasa niestandardowa, zaktualizuj klasę, aby dziedziczyć `IdentityRole<TKey>` . Przykład:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Zaktualizuj `ApplicationDbContext` , aby odwołać się do `ApplicationRole` klasy niestandardowej. Na przykład następująca Klasa odwołuje się do niestandardowego `ApplicationUser` i niestandardowego `ApplicationRole` :

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania Identity usługi w programie `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    W ASP.NET Core 2,1 lub nowszej Identity jest dostarczany jako Razor Biblioteka klas. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/scaffold-identity>. W związku z tym poprzedzający kod wymaga wywołania <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Jeśli Identity szkielet został użyty do dodawania Identity plików do projektu, Usuń wywołanie do `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania Identity usługi w programie `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Typ danych klucza podstawowego jest wywnioskowany przez analizowanie obiektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Zarejestrowanie niestandardowej klasy kontekstu bazy danych podczas dodawania Identity usługi w programie `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda akceptuje `TKey` Typ wskazujący typ danych klucza podstawowego.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Dodawanie właściwości nawigacji

Zmiana konfiguracji modelu dla relacji może być trudniejsza niż wprowadzanie innych zmian. Należy zwrócić uwagę, aby zastąpić istniejące relacje zamiast tworzyć nowe, dodatkowe relacje. W szczególności zmieniona relacja musi określać tę samą właściwość klucza obcego (FK) jak istniejąca relacja. Na przykład relacja między `Users` i `UserClaims` jest domyślnie określona w następujący sposób:

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

OBCY dla tej relacji jest określony jako `UserClaim.UserId` Właściwość. `HasMany`i `WithOne` są wywoływane bez argumentów, aby utworzyć relację bez właściwości nawigacji.

Dodaj właściwość nawigacji do `ApplicationUser` , która umożliwia `UserClaims` odwołanie od użytkownika:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

Wartość `TKey` dla `IdentityUserClaim<TKey>` jest typu określonego dla klucza PK dla użytkowników. W tym przypadku `TKey` jest to `string` spowodowane tym, że są używane wartości domyślne. To **nie** jest typ PK dla `UserClaim` typu jednostki.

Teraz, gdy istnieje właściwość nawigacji, należy ją skonfigurować w `OnModelCreating` :

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

Należy zauważyć, że relacja jest konfigurowana dokładnie tak, jak była wcześniej, tylko z właściwością nawigacji określoną w wywołaniu `HasMany` .

Właściwości nawigacji istnieją tylko w modelu EF, a nie w bazie danych. Ponieważ obcy dla relacji nie uległ zmianie, ten rodzaj zmiany modelu nie wymaga aktualizacji bazy danych. Można to sprawdzić przez dodanie migracji po dokonaniu zmiany. `Up`Metody i `Down` są puste.

### <a name="add-all-user-navigation-properties"></a>Dodaj wszystkie właściwości nawigacji użytkownika

Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje jednokierunkowe właściwości nawigacji dla wszystkich relacji na użytkowniku:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
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

### <a name="add-user-and-role-navigation-properties"></a>Dodawanie właściwości nawigacji użytkownika i roli

Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje właściwości nawigacji dla wszystkich relacji dla użytkownika i roli:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
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

Uwagi:

* Ten przykład zawiera również `UserRole` jednostkę join, która jest wymagana do nawigowania po relacji wiele-do-wielu od użytkowników do ról.
* Pamiętaj, aby zmienić typy właściwości nawigacji, aby odzwierciedlić, że `ApplicationXxx` typy są teraz używane zamiast `IdentityXxx` typów.
* Należy pamiętać, aby użyć `ApplicationXxx` w definicji generycznej `ApplicationContext` .

### <a name="add-all-navigation-properties"></a>Dodaj wszystkie właściwości nawigacji

Korzystając z powyższej sekcji jako wskazówki, Poniższy przykład konfiguruje właściwości nawigacji dla wszystkich relacji dla wszystkich typów jednostek:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
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

### <a name="use-composite-keys"></a>Użyj kluczy złożonych

Poprzednie sekcje przedstawiają zmianę typu klucza używanego w Identity modelu. Zmiana Identity modelu kluczy na używanie kluczy złożonych nie jest obsługiwana lub zalecana. Użycie klucza złożonego z Identity wymaga zmiany sposobu, w jaki Identity kod Menedżera współdziała z modelem. To dostosowanie wykracza poza zakres tego dokumentu.

### <a name="change-tablecolumn-names-and-facets"></a>Zmiana nazw tabel/kolumn i aspektów

Aby zmienić nazwy tabel i kolumn, wywołaj `base.OnModelCreating` . Następnie Dodaj konfigurację, aby zastąpić dowolne ustawienia domyślne. Na przykład, aby zmienić nazwę wszystkich Identity tabel:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

Te przykłady używają typów domyślnych Identity . Jeśli używasz typu aplikacji, takiego jak `ApplicationUser` , skonfiguruj ten typ zamiast typu domyślnego.

Poniższy przykład zmienia nazwy niektórych kolumn:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

Niektóre typy kolumn bazy danych można skonfigurować przy użyciu określonych *aspektów* (na przykład maksymalnej `string` dozwolonej długości). Poniższy przykład ustawia maksymalną długość kolumny dla kilku `string` właściwości w modelu:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>Mapuj na inny schemat

Schematy mogą działać inaczej niż dostawcy baz danych. W przypadku SQL Server wartością domyślną jest utworzenie wszystkich tabel w schemacie *dbo* . Tabele można tworzyć w innym schemacie. Przykład:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Ładowanie z opóźnieniem

W tej sekcji zostanie dodana obsługa serwerów proxy ładowania opóźnionego w Identity modelu. Ładowanie z opóźnieniem jest przydatne, ponieważ umożliwia korzystanie z właściwości nawigacji bez uprzedniego załadowania.

Typy jednostek mogą być odpowiednie do ładowania z opóźnieniem na kilka sposobów, zgodnie z opisem w [dokumentacji EF Core](/ef/core/querying/related-data#lazy-loading). Dla uproszczenia Użyj serwerów proxy ładowania z opóźnieniem, które wymagają:

* Instalacja pakietu [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Wywołanie <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> wewnątrz [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Publiczne typy jednostek z `public virtual` właściwościami nawigacji.

Poniższy przykład ilustruje wywoływanie `UseLazyLoadingProxies` w `Startup.ConfigureServices` :

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Zapoznaj się z powyższymi przykładami, aby uzyskać wskazówki dotyczące dodawania właściwości nawigacji do typów jednostek.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authentication/scaffold-identity>

::: moniker-end
