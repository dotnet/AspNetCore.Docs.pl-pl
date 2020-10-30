---
title: Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0 Identity
author: isaac2004
description: Dowiedz się, jak migrować istniejące aplikacje ASP.NET przy użyciu uwierzytelniania członkostwa do ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
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
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: d981c424fd2d6cad95b9164420f093672325c347
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051359"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>Migrowanie z uwierzytelniania członkostwa ASP.NET do ASP.NET Core 2,0 Identity

Autor [Tomasz Levin](https://isaaclevin.com)

W tym artykule przedstawiono Migrowanie schematu bazy danych dla aplikacji ASP.NET przy użyciu uwierzytelniania członkostwa w ASP.NET Core 2,0 Identity .

> [!NOTE]
> Ten dokument zawiera kroki niezbędne do migracji schematu bazy danych dla aplikacji opartych na członkostwie ASP.NET do schematu bazy danych używanego przez program ASP.NET Core Identity . Aby uzyskać więcej informacji na temat migrowania z uwierzytelniania opartego na członkostwie ASP.NET do usługi ASP.NET Identity , zobacz [Migrowanie istniejącej Identity aplikacji z członkostwa SQL do usługi ASP.NET ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Przegląd schematu członkostwa

Przed ASP.NET 2,0, deweloperzy byli poddani do tworzenia całego procesu uwierzytelniania i autoryzacji dla swoich aplikacji. W przypadku ASP.NET 2,0 wprowadzono członkostwo, które zapewnia standardowe rozwiązanie do obsługi zabezpieczeń w aplikacjach ASP.NET. Deweloperzy mogli teraz załadować schemat do SQL Server bazy danych za pomocą <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> polecenia. Po uruchomieniu tego polecenia następujące tabele zostały utworzone w bazie danych programu.

  ![Tabele członkostwa](identity/_static/membership-tables.png)

Aby przeprowadzić migrację istniejących aplikacji do ASP.NET Core 2,0 Identity , dane w tych tabelach muszą zostać zmigrowane do tabel używanych przez nowy Identity schemat.

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity Schemat 2,0

ASP.NET Core 2,0 jest zgodna z [Identity](/aspnet/identity/index) zasadą wprowadzoną w ASP.NET 4,5. Chociaż zasada jest współdzielona, implementacja między strukturami jest różna, nawet między wersjami ASP.NET Core (zobacz [Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

Najszybszym sposobem wyświetlenia schematu dla ASP.NET Core 2,0 Identity jest utworzenie nowej aplikacji ASP.NET Core 2,0. Wykonaj następujące kroki w programie Visual Studio 2017:

1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt** .
1. Utwórz nowy projekt **aplikacji sieci Web ASP.NET Core** o nazwie *Core Identity Sample* .
1. Wybierz pozycję **ASP.NET Core 2,0** na liście rozwijanej, a następnie wybierz pozycję **aplikacja sieci Web** . Ten szablon generuje aplikację [ Razor Pages](xref:razor-pages/index) . Przed kliknięciem przycisku **OK** kliknij pozycję **Zmień uwierzytelnianie** .
1. Wybierz **konta poszczególnych użytkowników** dla Identity szablonów. Na koniec kliknij przycisk **OK** , a następnie **OK** . Program Visual Studio tworzy projekt przy użyciu ASP.NET Core Identity szablonu.
1. Wybierz kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera** pakietów, aby otworzyć okno **konsoli Menedżera pakietów** (PMC).
1. Przejdź do katalogu głównego projektu w PMC i uruchom polecenie [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity używa EF Core do korzystania z bazy danych przechowującej dane uwierzytelniania. Aby nowo utworzona aplikacja działała, musi być bazą danych do przechowywania tych danych. Po utworzeniu nowej aplikacji najszybszym sposobem na sprawdzenie schematu w środowisku bazy danych jest utworzenie bazy danych przy użyciu [EF Core migracji](/ef/core/managing-schemas/migrations/). Ten proces powoduje utworzenie bazy danych lokalnie lub w innym miejscu, która śladuje ten schemat. Zapoznaj się z powyższą dokumentacją, aby uzyskać więcej informacji.

    Polecenia EF Core używają parametrów połączenia dla bazy danych określonej w *appsettings.json* . Następujące parametry połączenia są przeznaczone dla bazy danych na *hoście lokalnym* o nazwie *ASP-NET-Core-Identity* . W tym ustawieniu EF Core jest skonfigurowany do używania `DefaultConnection` parametrów połączenia.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Wybierz pozycję **Wyświetl**  >  **Eksplorator obiektów SQL Server** . Rozwiń węzeł odpowiadający nazwie bazy danych określonej we `ConnectionStrings:DefaultConnection` właściwości *appsettings.json* .

    `Update-Database`Polecenie utworzyło bazę danych określoną za pomocą schematu i wszystkie dane potrzebne do zainicjowania aplikacji. Na poniższej ilustracji przedstawiono strukturę tabeli, która została utworzona z poprzednimi krokami.

    ![::: No-Loc (tożsamość)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrowanie schematu

Istnieją delikatne różnice w strukturach tabel i polach dla członkostwa i ASP.NET Core Identity . Wzorzec został znacząco zmieniony na potrzeby uwierzytelniania/autoryzacji za pomocą aplikacji ASP.NET i ASP.NET Core. Obiekty kluczowe, które są nadal używane w połączeniu z Identity *użytkownikami* i *rolami* . Oto Mapowanie tabel dla *użytkowników* , *ról* i *roli użytkownika* .

### <a name="users"></a>Użytkownicy

|Identity<br>( `dbo.AspNetUsers` ) kolumna  |Typ     |Członkostwo<br>( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) kolumna|Typ      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Nie wszystkie mapowania pól są podobne do relacji jeden-do-jednego z członkostwa do ASP.NET Core Identity . Powyższa tabela przyjmuje domyślny schemat użytkownika członkostwa i mapuje go na ASP.NET Core Identity schemat. Wszystkie inne pola niestandardowe, które były używane na potrzeby członkostwa, muszą być mapowane ręcznie. W przypadku tego mapowania nie ma mapy haseł, ponieważ zarówno kryterium hasła, jak i sole hasła nie są migrowane między nimi. **Zaleca się pozostawienie hasła jako wartości null i poproszenie użytkowników o zresetowanie haseł.** W ASP.NET Core Identity programie `LockoutEnd` należy ustawić na pewną datę w przyszłości, jeśli użytkownik jest zablokowany. Jest to pokazane w skrypcie migracji.

### <a name="roles"></a>Role

|Identity<br>( `dbo.AspNetRoles` ) kolumna|Typ|Członkostwo<br>( `dbo.aspnet_Roles` ) kolumna|Typ|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>Role użytkowników

|Identity<br>( `dbo.AspNetUserRoles` ) kolumna|Typ|Członkostwo<br>( `dbo.aspnet_UsersInRoles` ) kolumna|Typ|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

Odwołuje się do powyższej tabeli mapowania podczas tworzenia skryptu migracji dla *użytkowników* i *ról* . W poniższym przykładzie założono, że masz dwie bazy danych na serwerze bazy danych. Jedna baza danych zawiera istniejący schemat i dane członkostwa ASP.NET. Druga *podstawowa Identity Przykładowa* baza danych została utworzona przy użyciu opisanej wcześniej procedury. Komentarze są zawarte w tekście, aby uzyskać więcej szczegółów.

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

Po zakończeniu poprzedniego skryptu ASP.NET Core Identity utworzona wcześniej aplikacja zostanie wypełniona użytkownikami członkostwa. Użytkownicy muszą zmienić swoje hasła przed zalogowaniem się.

> [!NOTE]
> Jeśli system członkostwa ma użytkowników z nazwami użytkowników, którzy nie są zgodni z ich adresem e-mail, zmiany są wymagane w przypadku aplikacji utworzonej wcześniej w celu tego celu. Szablon domyślny oczekuje `UserName` i jest taki `Email` sam. W sytuacjach, w których są różne, proces logowania należy zmodyfikować do użycia `UserName` zamiast `Email` .

Na `PageModel` stronie logowania, która znajduje się w *Pages\Account\Login.cshtml.cs* , Usuń `[EmailAddress]` atrybut z właściwości *email* . Zmień nazwę na nazwę *użytkownika* . Wymaga to zmiany wszędzie tam `EmailAddress` , gdzie jest wymieniony, w *widoku* i *PageModel* . Wynik będzie wyglądać następująco:

 ![Stałe logowanie](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak przenieść użytkowników z członkostwa SQL do ASP.NET Core 2,0 Identity . Aby uzyskać więcej informacji na temat ASP.NET Core Identity , zobacz [wprowadzenie do Identity ](xref:security/authentication/identity).
