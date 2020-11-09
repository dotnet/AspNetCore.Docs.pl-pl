---
title: 'Szkielet Identity w projektach ASP.NET Core'
author: rick-anderson
description: 'Dowiedz się, jak szkieletować Identity w projekcie ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141498"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="95ec8-103">Szkielet Identity w projektach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95ec8-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="95ec8-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95ec8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95ec8-105">ASP.NET Core udostępnia [ASP.NET Core Identity](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="95ec8-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="95ec8-106">Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="95ec8-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="95ec8-107">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="95ec8-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="95ec8-108">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="95ec8-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="95ec8-109">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="95ec8-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="95ec8-110">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać RCL domyślnego, zobacz sekcję [Tworzenie pełnego Identity źródła interfejsu użytkownika](#full).</span><span class="sxs-lookup"><span data-stu-id="95ec8-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="95ec8-111">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="95ec8-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="95ec8-112">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="95ec8-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="95ec8-113">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="95ec8-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="95ec8-114">W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="95ec8-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="95ec8-115">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="95ec8-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="95ec8-116">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="95ec8-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="95ec8-117">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="95ec8-118">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="95ec8-119">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="95ec8-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="95ec8-120">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="95ec8-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="95ec8-121">Podczas tworzenia szkieletu Identity z nowym kontekstem danych w projekcie z istniejącymi kontami:</span><span class="sxs-lookup"><span data-stu-id="95ec8-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="95ec8-122">W programie `Startup.ConfigureServices` Usuń wywołania do:</span><span class="sxs-lookup"><span data-stu-id="95ec8-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="95ec8-123">Na przykład `AddDbContext` i `AddDefaultIdentity` są oznaczone komentarzem w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="95ec8-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="95ec8-124">Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/ Identity / Identity HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="95ec8-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="95ec8-125">Zazwyczaj aplikacje, które zostały utworzone przy użyciu pojedynczych kont, powinny \* **nie** _ utworzyć nowego kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="95ec8-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="95ec8-126">Tworzenie szkieletu Identity w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="95ec8-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="95ec8-127">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="95ec8-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="95ec8-128">Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="95ec8-129">Identityjest skonfigurowany w _Areas/ Identity / Identity HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="95ec8-129">Identity is configured in _Areas/Identity/IdentityHostingStartup.cs\*.</span></span> <span data-ttu-id="95ec8-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="95ec8-131">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="95ec8-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="95ec8-132">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="95ec8-132">Enable authentication</span></span>

<span data-ttu-id="95ec8-133">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="95ec8-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="95ec8-134">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="95ec8-134">Layout changes</span></span>

<span data-ttu-id="95ec8-135">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="95ec8-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="95ec8-136">Tworzenie szkieletu Identity w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="95ec8-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="95ec8-137">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-138">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="95ec8-139">Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="95ec8-140">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="95ec8-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="95ec8-141">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="95ec8-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="95ec8-142">Identityjest skonfigurowany w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-143">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="95ec8-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="95ec8-144">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="95ec8-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="95ec8-145">Tworzenie szkieletu Identity w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="95ec8-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="95ec8-146">Tworzenie szkieletu Identity w Blazor Server projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="95ec8-147">Identityjest skonfigurowany w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-148">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="95ec8-149">Migracje</span><span class="sxs-lookup"><span data-stu-id="95ec8-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="95ec8-150">Przekaż token XSRF do aplikacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="95ec8-151">Tokeny mogą być przesyłane do składników:</span><span class="sxs-lookup"><span data-stu-id="95ec8-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="95ec8-152">Gdy tokeny uwierzytelniania są inicjowane i zapisywane w ramach uwierzytelniania cookie , można je przekazywać do składników programu.</span><span class="sxs-lookup"><span data-stu-id="95ec8-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="95ec8-153">Razor składniki nie mogą być używane `HttpContext` bezpośrednio, dlatego nie ma możliwości uzyskania [tokenu fałszerstwa (XSRF)](xref:security/anti-request-forgery) w celu opublikowania w Identity punkcie końcowym wylogowywania `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="95ec8-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="95ec8-154">Token XSRF można przesłać do składników.</span><span class="sxs-lookup"><span data-stu-id="95ec8-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="95ec8-155">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="95ec8-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="95ec8-156">W pliku *Pages/_Host. cshtml* Ustanów token po dodaniu go do `InitialApplicationState` `TokenProvider` klas i:</span><span class="sxs-lookup"><span data-stu-id="95ec8-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="95ec8-157">Zaktualizuj `App` składnik ( *App. Razor* ), aby przypisać `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="95ec8-158">Usługa przedstawiona `TokenProvider` w temacie jest używana w `LoginDisplay` składniku w poniższej sekcji [zmiany układu i przepływu uwierzytelniania](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="95ec8-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="95ec8-159">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="95ec8-159">Enable authentication</span></span>

<span data-ttu-id="95ec8-160">W `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="95ec8-160">In the `Startup` class:</span></span>

* <span data-ttu-id="95ec8-161">Potwierdź, że Razor usługi stron są dodawane w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="95ec8-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="95ec8-162">W przypadku korzystania z [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Zarejestruj usługę.</span><span class="sxs-lookup"><span data-stu-id="95ec8-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="95ec8-163">Zadzwoń do programu `UseDatabaseErrorPage` Application Builder w `Startup.Configure` środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="95ec8-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="95ec8-164">Wywołanie `UseAuthentication` i `UseAuthorization` po `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="95ec8-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="95ec8-165">Dodaj punkt końcowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="95ec8-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="95ec8-166">Zmiany przepływu układu i uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="95ec8-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="95ec8-167">Dodaj `RedirectToLogin` składnik ( *RedirectToLogin. Razor* ) do folderu *udostępnionego* aplikacji w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="95ec8-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

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

Dodaj `LoginDisplay` składnik ( *LoginDisplay. Razor* ) do folderu *udostępnionego* aplikacji. <span data-ttu-id="95ec8-169">[Usługa TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) udostępnia token XSRF formularza HTML, który jest wysyłany do Identity punktu końcowego wylogowania:</span><span class="sxs-lookup"><span data-stu-id="95ec8-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="95ec8-170">W `MainLayout` składniku ( *Shared/MainLayout. Razor* ) Dodaj `LoginDisplay` składnik do zawartości elementu najwyższego wiersza `<div>` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="95ec8-171">Punkty końcowe uwierzytelniania stylu</span><span class="sxs-lookup"><span data-stu-id="95ec8-171">Style authentication endpoints</span></span>

<span data-ttu-id="95ec8-172">Ponieważ Blazor Server Razor program używa Identity stron stron, style interfejsu użytkownika zmieniają się, gdy użytkownik przechodzi między Identity stronami i składnikami.</span><span class="sxs-lookup"><span data-stu-id="95ec8-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="95ec8-173">Dostępne są dwie opcje umożliwiające zaadresowanie stylów Incongruous:</span><span class="sxs-lookup"><span data-stu-id="95ec8-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="95ec8-174">IdentitySkładniki kompilacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-174">Build Identity components</span></span>

<span data-ttu-id="95ec8-175">Podejście do używania składników Identity zamiast stron polega na tworzeniu Identity składników.</span><span class="sxs-lookup"><span data-stu-id="95ec8-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="95ec8-176">Ponieważ `SignInManager` i `UserManager` nie są obsługiwane w Razor składnikach, użyj punktów końcowych interfejsu API w Blazor Server aplikacji, aby przetworzyć akcje konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="95ec8-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="95ec8-177">Używanie układu niestandardowego ze Blazor stylami aplikacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="95ec8-178">IdentityUkład stron i style można modyfikować, aby utworzyć strony używające Blazor motywu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="95ec8-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="95ec8-179">Przykład w tej sekcji jest jedynie punktem wyjścia do dostosowania.</span><span class="sxs-lookup"><span data-stu-id="95ec8-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="95ec8-180">Dodatkowe czynności są prawdopodobnie wymagane do osiągnięcia najlepszego środowiska użytkownika.</span><span class="sxs-lookup"><span data-stu-id="95ec8-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="95ec8-181">Utwórz nowy `NavMenu_IdentityLayout` składnik ( *Shared/NavMenu_ Identity Layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="95ec8-181">Create a new `NavMenu_IdentityLayout` component ( *Shared/NavMenu_IdentityLayout.razor* ).</span></span> <span data-ttu-id="95ec8-182">W przypadku znaczników i kodu składnika Użyj tej samej zawartości `NavMenu` składnika aplikacji ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="95ec8-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="95ec8-183">`NavLink`Przełączaj wszystkie elementy s do składników, których nie można uzyskać anonimowo, ponieważ Automatyczne przekierowania w `RedirectToLogin` składniku kończą się niepowodzeniem dla składników wymagających uwierzytelniania lub autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="95ec8-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="95ec8-184">W pliku *Pages/Shared/Layout. cshtml* wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="95ec8-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="95ec8-185">Dodaj Razor dyrektywy na początku pliku, aby używać pomocników tagów i składników aplikacji w folderze *udostępnionym* :</span><span class="sxs-lookup"><span data-stu-id="95ec8-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="95ec8-186">Zamień `{APPLICATION ASSEMBLY}` na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="95ec8-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="95ec8-187">Dodaj `<base>` tag i Blazor arkusz stylów `<link>` do `<head>` zawartości:</span><span class="sxs-lookup"><span data-stu-id="95ec8-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="95ec8-188">Zmień zawartość `<body>` tagu na następującą:</span><span class="sxs-lookup"><span data-stu-id="95ec8-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="95ec8-189">Tworzenie szkieletu Identity w Blazor Server projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="95ec8-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="95ec8-190">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-191">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="95ec8-192">Aplikacje autonomiczne lub hostowane Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="95ec8-192">Standalone or hosted Blazor WebAssembly apps</span></span>

<span data-ttu-id="95ec8-193">Aplikacje po stronie klienta Blazor WebAssembly używają własnych Identity metod interfejsu użytkownika i nie mogą używać ASP.NET Core Identity szkieletów.</span><span class="sxs-lookup"><span data-stu-id="95ec8-193">Client-side Blazor WebAssembly apps use their own Identity UI approaches and can't use ASP.NET Core Identity scaffolding.</span></span> <span data-ttu-id="95ec8-194">Po stronie serwera ASP.NET Core aplikacje rozwiązań hostowanych Blazor mogą postępować zgodnie ze Razor wskazówkami dotyczącymi stron/MVC w tym artykule i są skonfigurowane tak jak każdy inny typ aplikacji ASP.NET Core, która obsługuje Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-194">Server-side ASP.NET Core apps of hosted Blazor solutions can follow the Razor Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports Identity.</span></span>

<span data-ttu-id="95ec8-195">BlazorStruktura nie obejmuje Razor wersji składników Identity stron interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="95ec8-195">The Blazor framework doesn't include Razor component versions of Identity UI pages.</span></span> <span data-ttu-id="95ec8-196">Identity Składniki interfejsu użytkownika Razor mogą być utworzone niestandardowo lub uzyskane z nieobsługiwanych źródeł innych firm.</span><span class="sxs-lookup"><span data-stu-id="95ec8-196">Identity UI Razor components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="95ec8-197">Aby uzyskać więcej informacji, zobacz [ Blazor zabezpieczenia i Identity artykuły](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="95ec8-197">For more information, see the [Blazor Security and Identity articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="95ec8-198">Utwórz pełne Identity Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="95ec8-198">Create full Identity UI source</span></span>

<span data-ttu-id="95ec8-199">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki** .</span><span class="sxs-lookup"><span data-stu-id="95ec8-199">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="95ec8-200">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="95ec8-200">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="95ec8-201">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="95ec8-201">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="95ec8-202">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="95ec8-202">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="95ec8-203">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="95ec8-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="95ec8-204">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="95ec8-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="95ec8-205">Konfiguracja hasła</span><span class="sxs-lookup"><span data-stu-id="95ec8-205">Password configuration</span></span>

<span data-ttu-id="95ec8-206">Jeśli <xref:Microsoft.AspNetCore.Identity.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-206">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="95ec8-207">`InputModel``Password`właściwości znajdują się w następujących plikach:</span><span class="sxs-lookup"><span data-stu-id="95ec8-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="95ec8-208">Wyłącz stronę</span><span class="sxs-lookup"><span data-stu-id="95ec8-208">Disable a page</span></span>

<span data-ttu-id="95ec8-209">W tych sekcjach pokazano, jak wyłączyć stronę rejestracji, ale podejście może być używane do wyłączania dowolnej strony.</span><span class="sxs-lookup"><span data-stu-id="95ec8-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="95ec8-210">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="95ec8-210">To disable user registration:</span></span>

* <span data-ttu-id="95ec8-211">Szkielet Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-211">Scaffold Identity.</span></span> <span data-ttu-id="95ec8-212">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="95ec8-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="95ec8-213">Przykład:</span><span class="sxs-lookup"><span data-stu-id="95ec8-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="95ec8-214">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="95ec8-214">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="95ec8-215">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="95ec8-215">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="95ec8-216">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="95ec8-216">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="95ec8-217">Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-217">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="95ec8-218">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="95ec8-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="95ec8-219">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="95ec8-220">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-220">Use another app to add users</span></span>

<span data-ttu-id="95ec8-221">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="95ec8-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="95ec8-222">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="95ec8-222">Options to add users include:</span></span>

* <span data-ttu-id="95ec8-223">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="95ec8-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="95ec8-224">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="95ec8-224">A console app.</span></span>

<span data-ttu-id="95ec8-225">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="95ec8-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="95ec8-226">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="95ec8-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="95ec8-227">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="95ec8-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="95ec8-228">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95ec8-228">The user is added to the Identity database.</span></span>
* <span data-ttu-id="95ec8-229">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="95ec8-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="95ec8-230">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="95ec8-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="95ec8-231">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="95ec8-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="95ec8-232">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="95ec8-232">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="95ec8-233">Aby uniemożliwić publikowanie statycznych Identity zasobów w katalogu głównym sieci Web, zobacz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="95ec8-233">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95ec8-234">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95ec8-234">Additional resources</span></span>

* [<span data-ttu-id="95ec8-235">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="95ec8-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95ec8-236">ASP.NET Core 2,1 i nowszy stanowi [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="95ec8-236">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="95ec8-237">Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="95ec8-237">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="95ec8-238">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="95ec8-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="95ec8-239">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="95ec8-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="95ec8-240">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="95ec8-240">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="95ec8-241">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="95ec8-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="95ec8-242">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="95ec8-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="95ec8-243">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="95ec8-243">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="95ec8-244">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="95ec8-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="95ec8-245">W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="95ec8-245">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="95ec8-246">Po Identity uruchomieniu szkieletu tworzony jest plik *ScaffoldingReadme.txt* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="95ec8-246">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="95ec8-247">Plik *ScaffoldingReadme.txt* zawiera ogólne instrukcje dotyczące tego, co jest potrzebne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="95ec8-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="95ec8-248">Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="95ec8-249">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="95ec8-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="95ec8-250">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="95ec8-250">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="95ec8-251">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="95ec8-252">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-252">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="95ec8-253">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="95ec8-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="95ec8-254">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="95ec8-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="95ec8-255">Tworzenie szkieletu Identity w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="95ec8-255">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="95ec8-256">Dodaj następujące wyróżnione wywołania do `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="95ec8-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="95ec8-257">Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-257">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="95ec8-258">Identityjest skonfigurowany w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-258">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-259">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="95ec8-260">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="95ec8-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="95ec8-261">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="95ec8-261">Enable authentication</span></span>

<span data-ttu-id="95ec8-262">W `Configure` metodzie `Startup` klasy Wywołaj <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="95ec8-263">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="95ec8-263">Layout changes</span></span>

<span data-ttu-id="95ec8-264">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="95ec8-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="95ec8-265">Tworzenie szkieletu Identity w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="95ec8-265">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="95ec8-266">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-266">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-267">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="95ec8-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="95ec8-268">Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-268">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="95ec8-269">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="95ec8-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="95ec8-270">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="95ec8-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="95ec8-271">Identityjest skonfigurowany w *obszarach/ Identity / Identity HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-271">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="95ec8-272">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="95ec8-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="95ec8-273">Wywołaj <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="95ec8-274">Tworzenie szkieletu Identity w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="95ec8-274">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="95ec8-275">Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="95ec8-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="95ec8-276">Utwórz pełne Identity Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="95ec8-276">Create full Identity UI source</span></span>

<span data-ttu-id="95ec8-277">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki** .</span><span class="sxs-lookup"><span data-stu-id="95ec8-277">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="95ec8-278">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="95ec8-278">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="95ec8-279">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="95ec8-279">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="95ec8-280">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="95ec8-280">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="95ec8-281">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="95ec8-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="95ec8-282">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="95ec8-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="95ec8-283">Konfiguracja hasła</span><span class="sxs-lookup"><span data-stu-id="95ec8-283">Password configuration</span></span>

<span data-ttu-id="95ec8-284">Jeśli <xref:Microsoft.AspNetCore.Identity.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-284">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="95ec8-285">`InputModel``Password`właściwości znajdują się w następujących plikach:</span><span class="sxs-lookup"><span data-stu-id="95ec8-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="95ec8-286">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="95ec8-286">Disable register page</span></span>

<span data-ttu-id="95ec8-287">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="95ec8-287">To disable user registration:</span></span>

* <span data-ttu-id="95ec8-288">Szkielet Identity .</span><span class="sxs-lookup"><span data-stu-id="95ec8-288">Scaffold Identity.</span></span> <span data-ttu-id="95ec8-289">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="95ec8-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="95ec8-290">Przykład:</span><span class="sxs-lookup"><span data-stu-id="95ec8-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="95ec8-291">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="95ec8-291">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="95ec8-292">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="95ec8-292">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="95ec8-293">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="95ec8-293">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="95ec8-294">Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="95ec8-294">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="95ec8-295">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="95ec8-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="95ec8-296">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="95ec8-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="95ec8-297">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="95ec8-297">Use another app to add users</span></span>

<span data-ttu-id="95ec8-298">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="95ec8-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="95ec8-299">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="95ec8-299">Options to add users include:</span></span>

* <span data-ttu-id="95ec8-300">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="95ec8-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="95ec8-301">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="95ec8-301">A console app.</span></span>

<span data-ttu-id="95ec8-302">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="95ec8-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="95ec8-303">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="95ec8-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="95ec8-304">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="95ec8-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="95ec8-305">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="95ec8-305">The user is added to the Identity database.</span></span>
* <span data-ttu-id="95ec8-306">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="95ec8-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="95ec8-307">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="95ec8-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="95ec8-308">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="95ec8-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95ec8-309">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="95ec8-309">Additional resources</span></span>

* [<span data-ttu-id="95ec8-310">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="95ec8-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
