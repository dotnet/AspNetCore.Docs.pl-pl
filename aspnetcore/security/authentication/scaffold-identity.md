---
title: Szkielet Identity w projektach ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak szkieletować Identity w projekcie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3314458a504af7f44dcdc276de890fa9485a2b3
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103037"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Szkielet Identity w projektach ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core zapewnia [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class). Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL). Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie. Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji. Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL. Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać RCL domyślnego, zobacz sekcję [Tworzenie pełnego Identity źródła interfejsu użytkownika](#full).

Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL. Dostępna jest opcja wybierania Identity kodu do wygenerowania.

Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces. W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.

Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian. Sprawdź zmiany po uruchomieniu Identity szkieletu.

Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity . Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity . Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie. Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).

Podczas tworzenia szkieletu Identity z nowym kontekstem danych w projekcie z istniejącymi kontami:

* W programie `Startup.ConfigureServices` Usuń wywołania do:
  * `AddDbContext`
  * `AddDefaultIdentity`

Na przykład `AddDbContext` i `AddDefaultIdentity` są oznaczone komentarzem w następującym kodzie:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/ Identity /IdentityHostingStartup.cs*

Zazwyczaj aplikacje utworzone przy użyciu poszczególnych kont ***nie*** powinny tworzyć nowego kontekstu danych.

## <a name="scaffold-identity-into-an-empty-project"></a>Tworzenie szkieletu Identity w pustym projekcie

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityjest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migracje, UseAuthentication i układ

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Włącz uwierzytelnianie

Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Zmiany układu

Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Tworzenie szkieletu Identity w Razor projekcie z autoryzacją

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*

Identityjest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Tworzenie szkieletu Identity w projekcie MVC z autoryzacją

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a>Tworzenie szkieletu Identity w Blazor projekcie serwera bez istniejącej autoryzacji

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityjest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migracje

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Przekaż token XSRF do aplikacji

Tokeny mogą być przesyłane do składników:

* Gdy tokeny uwierzytelniania są inicjowane i zapisywane w pliku cookie uwierzytelniania, można je przesłać do składników programu.
* Razorskładniki nie mogą być używane `HttpContext` bezpośrednio, dlatego nie ma możliwości uzyskania [tokenu fałszerstwa (XSRF)](xref:security/anti-request-forgery) w celu opublikowania w Identity punkcie końcowym wylogowywania `/Identity/Account/Logout` . Token XSRF można przesłać do składników.

Aby uzyskać więcej informacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

W pliku *Pages/_Host. cshtml* Ustanów token po dodaniu go do `InitialApplicationState` `TokenProvider` klas i:

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Zaktualizuj `App` składnik (*App. Razor*), aby przypisać `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

Usługa przedstawiona `TokenProvider` w temacie jest używana w `LoginDisplay` składniku w poniższej sekcji [zmiany układu i przepływu uwierzytelniania](#layout-and-authentication-flow-changes) .

### <a name="enable-authentication"></a>Włącz uwierzytelnianie

W `Startup` klasie:

* Potwierdź, że Razor usługi stron są dodawane w programie `Startup.ConfigureServices` .
* W przypadku korzystania z [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Zarejestruj usługę.
* Zadzwoń do programu `UseDatabaseErrorPage` Application Builder w `Startup.Configure` środowisku deweloperskim.
* Wywołanie `UseAuthentication` i `UseAuthorization` po `UseRouting` .
* Dodaj punkt końcowy dla Razor stron.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Zmiany przepływu układu i uwierzytelniania

Dodaj `RedirectToLogin` składnik (*RedirectToLogin. Razor*) do folderu *udostępnionego* aplikacji w katalogu głównym projektu:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Dodaj `LoginDisplay` składnik (*LoginDisplay. Razor*) do folderu *udostępnionego* aplikacji. [Usługa TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) udostępnia token XSRF formularza HTML, który jest wysyłany do Identity punktu końcowego wylogowania:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

W `MainLayout` składniku (*Shared/MainLayout. Razor*) Dodaj `LoginDisplay` składnik do zawartości elementu najwyższego wiersza `<div>` :

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Punkty końcowe uwierzytelniania stylu

Ponieważ Blazor serwer używa Razor Identity stron stron, style interfejsu użytkownika zmieniają się, gdy użytkownik przechodzi między Identity stronami i składnikami. Dostępne są dwie opcje umożliwiające zaadresowanie stylów Incongruous:

#### <a name="build-identity-components"></a>IdentitySkładniki kompilacji

Podejście do używania składników Identity zamiast stron polega na tworzeniu Identity składników. Ponieważ `SignInManager` i `UserManager` nie są obsługiwane w Razor składnikach, użyj punktów końcowych interfejsu API w Blazor aplikacji serwera, aby przetworzyć akcje konta użytkownika.

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a>Używanie układu niestandardowego ze Blazor stylami aplikacji

IdentityUkład stron i style można modyfikować, aby utworzyć strony używające Blazor motywu domyślnego.

> [!NOTE]
> Przykład w tej sekcji jest jedynie punktem wyjścia do dostosowania. Dodatkowe czynności są prawdopodobnie wymagane do osiągnięcia najlepszego środowiska użytkownika.

Utwórz nowy `NavMenu_IdentityLayout` składnik (*Shared/NavMenu_IdentityLayout. Razor*). W przypadku znaczników i kodu składnika Użyj tej samej zawartości `NavMenu` składnika aplikacji (*Shared/NavMenu. Razor*). `NavLink`Przełączaj wszystkie elementy s do składników, których nie można uzyskać anonimowo, ponieważ Automatyczne przekierowania w `RedirectToLogin` składniku kończą się niepowodzeniem dla składników wymagających uwierzytelniania lub autoryzacji.

W pliku *Pages/Shared/Layout. cshtml* wprowadź następujące zmiany:

* Dodaj Razor dyrektywy na początku pliku, aby używać pomocników tagów i składników aplikacji w folderze *udostępnionym* :

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Zamień `{APPLICATION ASSEMBLY}` na nazwę zestawu aplikacji.

* Dodaj `<base>` tag i Blazor arkusz stylów `<link>` do `<head>` zawartości:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Zmień zawartość `<body>` tagu na następującą:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a>Tworzenie szkieletu Identity w Blazor projekcie serwera z autoryzacją

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Utwórz pełne Identity Źródło interfejsu użytkownika

Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.

Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1. Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Wartość domyślna Identity jest zastępowana w następującym kodzie:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zarejestruj `IEmailSender` implementację, na przykład:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a>Wyłącz stronę

W tych sekcjach pokazano, jak wyłączyć stronę rejestracji, ale podejście może być używane do wyłączania dowolnej strony.

Aby wyłączyć rejestrację użytkownika:

* Szkielet Identity . Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation. Przykład:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .

  * Usuń kod i linki z pliku cshtml.
  * Usuń kod potwierdzający z `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Dodawanie użytkowników przy użyciu innej aplikacji

Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web. Dostępne są następujące opcje dodawania użytkowników:

* Dedykowana aplikacja sieci Web administratora.
* Aplikacja konsolowa.

Poniższy kod przedstawia jedno podejście do dodawania użytkowników:

* Lista użytkowników jest odczytywana w pamięci.
* Dla każdego użytkownika jest generowany silny unikatowy hasło.
* Użytkownik zostanie dodany do Identity bazy danych.
* Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Poniższy kod zawiera opis dodawania użytkownika:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobne podejście może być stosowane w scenariuszach produkcyjnych.

## <a name="prevent-publish-of-static-identity-assets"></a>Zapobiegaj publikowaniu Identity zasobów statycznych

Aby uniemożliwić publikowanie statycznych Identity zasobów w katalogu głównym sieci Web, zobacz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 i nowsze udostępniają [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class). Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL). Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie. Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji. Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL. Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).

Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL. Dostępna jest opcja wybierania Identity kodu do wygenerowania.

Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces. W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.

Po Identity uruchomieniu szkieletu tworzony jest plik *ScaffoldingReadme.txt* w katalogu projektu. Plik *ScaffoldingReadme.txt* zawiera ogólne instrukcje dotyczące tego, co jest potrzebne do ukończenia Identity aktualizacji tworzenia szkieletów. Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme.txt* .

Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian. Sprawdź zmiany po uruchomieniu Identity szkieletu.

> [!NOTE]
> Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity . Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity . Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie. Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Tworzenie szkieletu Identity w pustym projekcie

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Dodaj następujące wyróżnione wywołania do `Startup` klasy:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityjest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migracje, UseAuthentication i układ

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Włącz uwierzytelnianie

W `Configure` metodzie `Startup` klasy Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Zmiany układu

Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Tworzenie szkieletu Identity w Razor projekcie z autoryzacją

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*

Identityjest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*. Aby uzyskać więcej informacji, zobacz IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Tworzenie szkieletu Identity w projekcie MVC z autoryzacją

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Utwórz pełne Identity Źródło interfejsu użytkownika

Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.

Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1. Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Wartość domyślna Identity jest zastępowana w następującym kodzie:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zarejestruj `IEmailSender` implementację, na przykład:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Wyłącz stronę rejestracji

Aby wyłączyć rejestrację użytkownika:

* Szkielet Identity . Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation. Przykład:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .

  * Usuń kod i linki z pliku cshtml.
  * Usuń kod potwierdzający z `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Dodawanie użytkowników przy użyciu innej aplikacji

Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web. Dostępne są następujące opcje dodawania użytkowników:

* Dedykowana aplikacja sieci Web administratora.
* Aplikacja konsolowa.

Poniższy kod przedstawia jedno podejście do dodawania użytkowników:

* Lista użytkowników jest odczytywana w pamięci.
* Dla każdego użytkownika jest generowany silny unikatowy hasło.
* Użytkownik zostanie dodany do Identity bazy danych.
* Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Poniższy kod zawiera opis dodawania użytkownika:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobne podejście może być stosowane w scenariuszach produkcyjnych.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
