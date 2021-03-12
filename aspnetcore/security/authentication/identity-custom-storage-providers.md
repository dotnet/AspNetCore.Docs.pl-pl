---
title: Niestandardowi dostawcy magazynu dla programu ASP.NET Core Identity
author: ardalis
description: Dowiedz się, jak skonfigurować niestandardowych dostawców magazynu dla programu ASP.NET Core Identity .
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
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
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: f1c4366e4e4afa3dd86a816a649ad0a8b2ce817b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588630"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Niestandardowi dostawcy magazynu dla programu ASP.NET Core Identity

Przez [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity to rozszerzalny system, który umożliwia utworzenie niestandardowego dostawcy magazynu i połączenie go z aplikacją. W tym temacie opisano sposób tworzenia niestandardowego dostawcy magazynu dla programu ASP.NET Core Identity . Dotyczy to ważnych koncepcji tworzenia własnego dostawcy magazynu, ale nie jest to przewodnik krok po kroku.

[Wyświetl lub Pobierz przykład z witryny GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample).

## <a name="introduction"></a>Wprowadzenie

Domyślnie ASP.NET Core Identity System przechowuje informacje o użytkowniku w bazie danych SQL Server przy użyciu Entity Framework Core. W przypadku wielu aplikacji to podejście działa prawidłowo. Jednak warto użyć innego mechanizmu trwałości lub schematu danych. Na przykład:

* Używasz [usługi Azure Table Storage](/azure/storage/) lub innego magazynu danych.
* Tabele bazy danych mają inną strukturę. 
* Możesz chcieć użyć innego podejścia do uzyskiwania dostępu do danych, takiego jak [Dapper](https://github.com/StackExchange/Dapper). 

W każdym z tych przypadków można napisać niestandardowego dostawcę dla mechanizmu magazynu i podłączyć tego dostawcę do aplikacji.

ASP.NET Core Identity jest uwzględniony w szablonach projektu w programie Visual Studio z opcją "indywidualne konta użytkowników".

Korzystając z interfejs wiersza polecenia platformy .NET Core, Dodaj `-au Individual` :

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core IdentityArchitektura

ASP.NET Core Identity składa się z klas o nazwie menedżerowie i sklepy. *Menedżerowie* są klasami wysokiego poziomu, których deweloperzy aplikacji używają do wykonywania operacji, takich jak tworzenie Identity użytkownika. *Magazyny* są klasy niższego poziomu, które określają, jak są utrwalane jednostki, takie jak użytkownicy i role. Sklepy są zgodne ze wzorcem repozytorium i są ściśle powiązane z mechanizmem trwałości. Menedżerowie są niezależni od sklepów, co oznacza, że można zastąpić mechanizm trwałości bez zmiany kodu aplikacji (z wyjątkiem konfiguracji).

Na poniższym diagramie przedstawiono, w jaki sposób aplikacja internetowa współdziała z menedżerami, a jednocześnie przechowuje interakcję z warstwą dostępu do danych.

![Aplikacje ASP.NET Core działają z menedżerami (na przykład "Usermanager", "roleManager"). Menedżerowie pracują z magazynami (na przykład "UserStore"), które komunikują się ze źródłem danych przy użyciu biblioteki, takiej jak Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Aby utworzyć niestandardowego dostawcę magazynu, Utwórz źródło danych, warstwę dostępu do danych i klasy magazynu, które współpracują z tą warstwą dostępu do danych (zielone i szare pola na powyższym diagramie). Nie musisz dostosowywać menedżerów ani kodu aplikacji, które współdziałają z nimi (niebieskie pola powyżej).

Podczas tworzenia nowego wystąpienia `UserManager` lub `RoleManager` podania typu klasy użytkownika i przekazania wystąpienia klasy magazynu jako argumentu. Takie podejście umożliwia podłączenie dostosowanych klas do ASP.NET Core. 

[Zmień konfigurację aplikacji, aby używała nowego dostawcy magazynu](#reconfigure-app-to-use-a-new-storage-provider) pokazuje, jak utworzyć wystąpienie `UserManager` i `RoleManager` przy użyciu dostosowanego magazynu.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity przechowuje typy danych

[ASP.NET Core Identity](https://github.com/aspnet/identity) typy danych są szczegółowo opisane w następujących sekcjach:

### <a name="users"></a>Użytkownicy

Zarejestrowani użytkownicy witryny sieci Web. Typ [ Identity użytkownika](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) może być rozszerzony lub używany jako przykład dla własnego typu niestandardowego. Nie musisz dziedziczyć z określonego typu w celu zaimplementowania własnego niestandardowego rozwiązania do magazynowania tożsamości.

### <a name="user-claims"></a>Oświadczenia użytkownika

Zestaw instrukcji (lub [oświadczeń](/dotnet/api/system.security.claims.claim)) o użytkowniku, który reprezentuje tożsamość użytkownika. Można włączyć lepsze wyrażenie tożsamości użytkownika, niż można osiągnąć za pomocą ról.

### <a name="user-logins"></a>Logowania użytkowników

Informacje o zewnętrznym dostawcy uwierzytelniania (na przykład Facebook lub konto Microsoft) do użycia podczas logowania użytkownika. [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Role

Grupy autoryzacji dla witryny. Zawiera identyfikator roli i nazwę roli (na przykład "admin" lub "Employee"). [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Warstwa dostępu do danych

W tym temacie założono, że znasz mechanizm trwałości, który ma być używany, oraz sposób tworzenia jednostek dla tego mechanizmu. Ten temat nie zawiera szczegółowych informacji o sposobie tworzenia repozytoriów lub klas dostępu do danych; zawiera ona kilka sugestii dotyczących decyzji projektowych podczas pracy z programem ASP.NET Core Identity .

Podczas projektowania warstwy dostępu do danych dla niestandardowego dostawcy magazynu istnieje dużo swobody. Należy tylko utworzyć mechanizmy trwałości dla funkcji, które mają być używane w aplikacji. Jeśli na przykład nie korzystasz z ról w aplikacji, nie musisz tworzyć magazynu dla ról lub skojarzeń roli użytkownika. Twoja technologia i istniejąca infrastruktura mogą wymagać struktury, która różni się od domyślnej implementacji programu ASP.NET Core Identity . W warstwie dostępu do danych można zapewnić logikę do pracy ze strukturą wdrożenia magazynu.

Warstwa dostępu do danych umożliwia logikę zapisywania danych ze ASP.NET Core Identity źródła danych. Warstwa dostępu do danych dla niestandardowego dostawcy magazynu może zawierać następujące klasy służące do przechowywania informacji o użytkowniku i roli.

### <a name="context-class"></a>Context — Klasa

Hermetyzuje informacje w celu nawiązania połączenia z mechanizmem trwałości i wykonywania zapytań. Kilka klas danych wymaga wystąpienia tej klasy, zwykle udostępnianych przez iniekcję zależności. [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Magazyn użytkowników

Przechowuje i pobiera informacje o użytkowniku (takie jak nazwa użytkownika i skrót hasła). [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Magazyn ról

Przechowuje i pobiera informacje o rolach (takie jak nazwa roli). [Przykład](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Magazyn UserClaims

Przechowuje i pobiera informacje o użytkowniku (takie jak typ i wartość żądania). [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Magazyn UserLogins

Przechowuje i pobiera informacje logowania użytkownika (na przykład zewnętrzny dostawca uwierzytelniania). [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Przestrzeń dyskowa UserRole

Przechowuje i pobiera przypisane role, do których użytkownicy. [Przykład](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Porada:** Zaimplementuj tylko klasy, które mają być używane w aplikacji.

W klasach dostępu do danych podaj kod służący do wykonywania operacji na danych dla mechanizmu trwałości. Na przykład w ramach niestandardowego dostawcy może istnieć następujący kod, aby utworzyć nowego użytkownika w klasie *magazynu* :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Logika implementacji dla tworzenia użytkownika znajduje się w `_usersTable.CreateAsync` metodzie, jak pokazano poniżej.

## <a name="customize-the-user-class"></a>Dostosowywanie klasy użytkownika

Podczas implementowania dostawcy magazynu należy utworzyć klasę użytkownika, która jest równoważna z [ Identity klasą użytkownika](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Co najmniej Klasa użytkownika musi zawierać `Id` `UserName` Właściwość i.

`IdentityUser`Klasa definiuje właściwości, które są `UserManager` wywoływane podczas wykonywania żądanych operacji. Domyślny typ `Id` właściwości jest ciągiem, ale można dziedziczyć po `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` i określić inny typ. Platforma oczekuje implementacji magazynu do obsługi konwersji typu danych.

## <a name="customize-the-user-store"></a>Dostosowywanie magazynu użytkowników

Utwórz `UserStore` klasę, która dostarcza metody dla wszystkich operacji na danych użytkownika. Ta klasa jest równoważna z klasą [UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . W `UserStore` klasie należy zaimplementować `IUserStore<TUser>` i opcjonalnie wymagane interfejsy. Należy wybrać opcjonalne interfejsy do wdrożenia w oparciu o funkcje dostępne w aplikacji.

### <a name="optional-interfaces"></a>Interfejsy opcjonalne

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

Opcjonalne interfejsy dziedziczą z `IUserStore<TUser>` . W [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)widzisz częściowo zaimplementowany przykładowy magazyn użytkowników.

W ramach `UserStore` klasy używane są klasy dostępu do danych, które zostały utworzone w celu wykonania operacji. Są one przenoszone przy użyciu iniekcji zależności. Na przykład w SQL Server z implementacją Dapper `UserStore` Klasa ma `CreateAsync` metodę, która używa wystąpienia `DapperUsersTable` do wstawienia nowego rekordu:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Interfejsy do wdrożenia podczas dostosowywania magazynu użytkownika

* **IUserStore**  
 Interfejs [IUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) jest jedynym interfejsem, który należy zaimplementować w magazynie użytkownika. Definiuje on metody tworzenia, aktualizowania, usuwania i pobierania użytkowników.
* **IUserClaimStore**  
 Interfejs [IUserClaimStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) definiuje metody implementowane w celu włączenia oświadczeń użytkowników. Zawiera metody dodawania, usuwania i pobierania oświadczeń użytkowników.
* **IUserLoginStore**  
 [IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definiuje metody implementowane w celu umożliwienia zewnętrznym dostawcom uwierzytelniania. Zawiera metody dodawania, usuwania i pobierania identyfikatorów logowania użytkowników oraz metoda pobierania użytkownika na podstawie informacji logowania.
* **IUserRoleStore**  
 Interfejs [IUserRoleStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) definiuje metody implementowane w celu zamapowania użytkownika na rolę. Zawiera on metody dodawania, usuwania i pobierania ról użytkownika oraz metodę sprawdzania, czy użytkownik jest przypisany do roli.
* **IUserPasswordStore**  
 Interfejs [IUserPasswordStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) definiuje metody implementowane w celu utrwalenia haseł z mieszaniem. Zawiera on metody pobierania i ustawiania skrótu hasła oraz metodę, która wskazuje, czy użytkownik ustawił hasło.
* **IUserSecurityStampStore**  
 Interfejs [IUserSecurityStampStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) definiuje metody implementowane w celu użycia sygnatury zabezpieczeń w celu wskazania, czy informacje o koncie użytkownika zostały zmienione. Ta sygnatura jest aktualizowana, gdy użytkownik zmienia hasło lub dodaje lub usuwa logowania. Zawiera on metody pobierania i ustawiania sygnatury zabezpieczeń.
* **IUserTwoFactorStore**  
 Interfejs [IUserTwoFactorStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) definiuje metody implementowane w celu obsługi uwierzytelniania dwuskładnikowego. Zawiera on metody umożliwiające pobieranie i określanie, czy uwierzytelnianie dwuskładnikowe jest włączone dla użytkownika.
* **IUserPhoneNumberStore**  
 Interfejs [IUserPhoneNumberStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) definiuje metody, które są implementowane w celu przechowywania numerów telefonów użytkowników. Zawiera on metody pobierania i ustawiania numeru telefonu oraz tego, czy numer telefonu został potwierdzony.
* **IUserEmailStore**  
 Interfejs [IUserEmailStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) definiuje metody implementowane w celu przechowywania adresów e-mail użytkowników. Zawiera on metody pobierania i ustawiania adresu e-mail oraz tego, czy wiadomość e-mail została potwierdzona.
* **IUserLockoutStore**  
 Interfejs [IUserLockoutStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) definiuje metody implementowane w celu przechowywania informacji o blokowaniu konta. Zawiera metody śledzenia nieudanych prób dostępu i blokad.
* **IQueryableUserStore**  
 Interfejs [IQueryableUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) definiuje elementy członkowskie, które są implementowane w celu udostępnienia magazynu użytkownika queryable.

Implementowane są tylko interfejsy, które są potrzebne w aplikacji. Na przykład:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, Identity UserLogin i Identity UserRole

`Microsoft.AspNet.Identity.EntityFramework`Przestrzeń nazw zawiera implementacje klas [ Identity UserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [ Identity UserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)i [ Identity UserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Jeśli używasz tych funkcji, możesz chcieć utworzyć własne wersje tych klas i zdefiniować właściwości aplikacji. Czasami jednak wydajniejsze jest, aby nie ładować tych jednostek do pamięci podczas wykonywania podstawowych operacji (takich jak dodawanie lub usuwanie roszczeń użytkownika). Zamiast tego klasy magazynu zaplecza mogą wykonywać te operacje bezpośrednio w źródle danych. Na przykład `UserStore.GetClaimsAsync` Metoda może wywołać `userClaimTable.FindByUserId(user.Id)` metodę, aby wykonać zapytanie bezpośrednio względem tej tabeli i zwrócić listę oświadczeń.

## <a name="customize-the-role-class"></a>Dostosowywanie klasy roli

Podczas implementowania dostawcy magazynu roli można utworzyć niestandardowy typ roli. Nie musi implementować określonego interfejsu, ale musi mieć, `Id` a zazwyczaj będzie miał `Name` Właściwość.

Poniżej przedstawiono przykładową klasę roli:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Dostosowywanie magazynu ról

Można utworzyć `RoleStore` klasę, która dostarcza metody dla wszystkich operacji na danych na rolach. Ta klasa jest równoważna z klasą [RoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . W `RoleStore` klasie należy zaimplementować `IRoleStore<TRole>` interfejs i opcjonalnie `IQueryableRoleStore<TRole>` .

* **IRoleStore &lt; TRole&gt;**  
 Interfejs [IRoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) definiuje metody do zaimplementowania w klasie magazynu ról. Zawiera on metody tworzenia, aktualizowania, usuwania i pobierania ról.
* **RoleStore &lt; TRole&gt;**  
 Aby dostosować `RoleStore` , należy utworzyć klasę, która implementuje `IRoleStore<TRole>` interfejs. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Zmień konfigurację aplikacji tak, aby korzystała z nowego dostawcy magazynu

Po zaimplementowaniu dostawcy magazynu należy skonfigurować aplikację do korzystania z niej. Jeśli aplikacja użyła domyślnego dostawcy, Zastąp ją własnym dostawcą.

1. Usuń `Microsoft.AspNetCore.EntityFramework.Identity` pakiet NuGet.
1. Jeśli dostawca magazynu znajduje się w osobnym projekcie lub pakiecie, Dodaj odwołanie do niego.
1. Zastąp wszystkie odwołania do za pomocą `Microsoft.AspNetCore.EntityFramework.Identity` instrukcji using dla przestrzeni nazw dostawcy magazynu.
1. W `ConfigureServices` metodzie Zmień metodę, `AddIdentity` tak aby korzystała z typów niestandardowych. W tym celu można utworzyć własne metody rozszerzenia. Zapoznaj się z przykładem [ Identity ServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Jeśli używasz ról, zaktualizuj, `RoleManager` Aby użyć `RoleStore` klasy.
1. Zaktualizuj parametry połączenia i poświadczenia do konfiguracji aplikacji.

Przykład:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Odwołania

* [Niestandardowi dostawcy magazynu dla ASP.NET 4. x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/main/src/Identity): To repozytorium zawiera linki do dostawców sklepu obsługiwanego przez społeczność.
