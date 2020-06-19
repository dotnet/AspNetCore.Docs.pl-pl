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
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="03d62-103">Szkielet Identity w projektach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03d62-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="03d62-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03d62-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03d62-105">ASP.NET Core zapewnia [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="03d62-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="03d62-106">Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="03d62-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="03d62-107">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="03d62-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="03d62-108">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="03d62-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="03d62-109">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="03d62-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="03d62-110">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać RCL domyślnego, zobacz sekcję [Tworzenie pełnego Identity źródła interfejsu użytkownika](#full).</span><span class="sxs-lookup"><span data-stu-id="03d62-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="03d62-111">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="03d62-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="03d62-112">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="03d62-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="03d62-113">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="03d62-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="03d62-114">W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="03d62-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="03d62-115">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="03d62-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="03d62-116">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="03d62-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="03d62-117">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="03d62-118">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="03d62-119">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="03d62-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="03d62-120">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="03d62-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="03d62-121">Podczas tworzenia szkieletu Identity z nowym kontekstem danych w projekcie z istniejącymi kontami:</span><span class="sxs-lookup"><span data-stu-id="03d62-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="03d62-122">W programie `Startup.ConfigureServices` Usuń wywołania do:</span><span class="sxs-lookup"><span data-stu-id="03d62-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="03d62-123">Na przykład `AddDbContext` i `AddDefaultIdentity` są oznaczone komentarzem w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="03d62-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="03d62-124">Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/ Identity /IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="03d62-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="03d62-125">Zazwyczaj aplikacje utworzone przy użyciu poszczególnych kont ***nie*** powinny tworzyć nowego kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="03d62-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="03d62-126">Tworzenie szkieletu Identity w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="03d62-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03d62-127">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="03d62-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="03d62-128">Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="03d62-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="03d62-129">jest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="03d62-131">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="03d62-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="03d62-132">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="03d62-132">Enable authentication</span></span>

<span data-ttu-id="03d62-133">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="03d62-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="03d62-134">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="03d62-134">Layout changes</span></span>

<span data-ttu-id="03d62-135">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="03d62-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="03d62-136">Tworzenie szkieletu Identity w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="03d62-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="03d62-137">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-138">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="03d62-139">Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="03d62-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="03d62-140">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="03d62-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="03d62-141">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="03d62-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="03d62-142">jest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-143">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="03d62-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="03d62-144">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="03d62-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="03d62-145">Tworzenie szkieletu Identity w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="03d62-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="03d62-146">Tworzenie szkieletu Identity w Blazor projekcie serwera bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="03d62-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="03d62-147">jest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-148">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="03d62-149">Migracje</span><span class="sxs-lookup"><span data-stu-id="03d62-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="03d62-150">Przekaż token XSRF do aplikacji</span><span class="sxs-lookup"><span data-stu-id="03d62-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="03d62-151">Tokeny mogą być przesyłane do składników:</span><span class="sxs-lookup"><span data-stu-id="03d62-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="03d62-152">Gdy tokeny uwierzytelniania są inicjowane i zapisywane w pliku cookie uwierzytelniania, można je przesłać do składników programu.</span><span class="sxs-lookup"><span data-stu-id="03d62-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="03d62-153">składniki nie mogą być używane `HttpContext` bezpośrednio, dlatego nie ma możliwości uzyskania [tokenu fałszerstwa (XSRF)](xref:security/anti-request-forgery) w celu opublikowania w Identity punkcie końcowym wylogowywania `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="03d62-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="03d62-154">Token XSRF można przesłać do składników.</span><span class="sxs-lookup"><span data-stu-id="03d62-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="03d62-155">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="03d62-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="03d62-156">W pliku *Pages/_Host. cshtml* Ustanów token po dodaniu go do `InitialApplicationState` `TokenProvider` klas i:</span><span class="sxs-lookup"><span data-stu-id="03d62-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="03d62-157">Zaktualizuj `App` składnik (*App. Razor*), aby przypisać `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="03d62-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="03d62-158">Usługa przedstawiona `TokenProvider` w temacie jest używana w `LoginDisplay` składniku w poniższej sekcji [zmiany układu i przepływu uwierzytelniania](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="03d62-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="03d62-159">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="03d62-159">Enable authentication</span></span>

<span data-ttu-id="03d62-160">W `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="03d62-160">In the `Startup` class:</span></span>

* <span data-ttu-id="03d62-161">Potwierdź, że Razor usługi stron są dodawane w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03d62-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="03d62-162">W przypadku korzystania z [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Zarejestruj usługę.</span><span class="sxs-lookup"><span data-stu-id="03d62-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="03d62-163">Zadzwoń do programu `UseDatabaseErrorPage` Application Builder w `Startup.Configure` środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="03d62-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="03d62-164">Wywołanie `UseAuthentication` i `UseAuthorization` po `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="03d62-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="03d62-165">Dodaj punkt końcowy dla Razor stron.</span><span class="sxs-lookup"><span data-stu-id="03d62-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="03d62-166">Zmiany przepływu układu i uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="03d62-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="03d62-167">Dodaj `RedirectToLogin` składnik (*RedirectToLogin. Razor*) do folderu *udostępnionego* aplikacji w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="03d62-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Dodaj `LoginDisplay` składnik (*LoginDisplay. Razor*) do folderu *udostępnionego* aplikacji. <span data-ttu-id="03d62-169">[Usługa TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) udostępnia token XSRF formularza HTML, który jest wysyłany do Identity punktu końcowego wylogowania:</span><span class="sxs-lookup"><span data-stu-id="03d62-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="03d62-170">W `MainLayout` składniku (*Shared/MainLayout. Razor*) Dodaj `LoginDisplay` składnik do zawartości elementu najwyższego wiersza `<div>` :</span><span class="sxs-lookup"><span data-stu-id="03d62-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="03d62-171">Punkty końcowe uwierzytelniania stylu</span><span class="sxs-lookup"><span data-stu-id="03d62-171">Style authentication endpoints</span></span>

<span data-ttu-id="03d62-172">Ponieważ Blazor serwer używa Razor Identity stron stron, style interfejsu użytkownika zmieniają się, gdy użytkownik przechodzi między Identity stronami i składnikami.</span><span class="sxs-lookup"><span data-stu-id="03d62-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="03d62-173">Dostępne są dwie opcje umożliwiające zaadresowanie stylów Incongruous:</span><span class="sxs-lookup"><span data-stu-id="03d62-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="03d62-174">IdentitySkładniki kompilacji</span><span class="sxs-lookup"><span data-stu-id="03d62-174">Build Identity components</span></span>

<span data-ttu-id="03d62-175">Podejście do używania składników Identity zamiast stron polega na tworzeniu Identity składników.</span><span class="sxs-lookup"><span data-stu-id="03d62-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="03d62-176">Ponieważ `SignInManager` i `UserManager` nie są obsługiwane w Razor składnikach, użyj punktów końcowych interfejsu API w Blazor aplikacji serwera, aby przetworzyć akcje konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03d62-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="03d62-177">Używanie układu niestandardowego ze Blazor stylami aplikacji</span><span class="sxs-lookup"><span data-stu-id="03d62-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="03d62-178">IdentityUkład stron i style można modyfikować, aby utworzyć strony używające Blazor motywu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="03d62-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="03d62-179">Przykład w tej sekcji jest jedynie punktem wyjścia do dostosowania.</span><span class="sxs-lookup"><span data-stu-id="03d62-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="03d62-180">Dodatkowe czynności są prawdopodobnie wymagane do osiągnięcia najlepszego środowiska użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03d62-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="03d62-181">Utwórz nowy `NavMenu_IdentityLayout` składnik (*Shared/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="03d62-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="03d62-182">W przypadku znaczników i kodu składnika Użyj tej samej zawartości `NavMenu` składnika aplikacji (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="03d62-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="03d62-183">`NavLink`Przełączaj wszystkie elementy s do składników, których nie można uzyskać anonimowo, ponieważ Automatyczne przekierowania w `RedirectToLogin` składniku kończą się niepowodzeniem dla składników wymagających uwierzytelniania lub autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="03d62-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="03d62-184">W pliku *Pages/Shared/Layout. cshtml* wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="03d62-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="03d62-185">Dodaj Razor dyrektywy na początku pliku, aby używać pomocników tagów i składników aplikacji w folderze *udostępnionym* :</span><span class="sxs-lookup"><span data-stu-id="03d62-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="03d62-186">Zamień `{APPLICATION ASSEMBLY}` na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="03d62-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="03d62-187">Dodaj `<base>` tag i Blazor arkusz stylów `<link>` do `<head>` zawartości:</span><span class="sxs-lookup"><span data-stu-id="03d62-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="03d62-188">Zmień zawartość `<body>` tagu na następującą:</span><span class="sxs-lookup"><span data-stu-id="03d62-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="03d62-189">Tworzenie szkieletu Identity w Blazor projekcie serwera z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="03d62-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03d62-190">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-191">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="03d62-192">Utwórz pełne Identity Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="03d62-192">Create full Identity UI source</span></span>

<span data-ttu-id="03d62-193">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="03d62-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="03d62-194">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="03d62-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="03d62-195">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03d62-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="03d62-196">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="03d62-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="03d62-197">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="03d62-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="03d62-198">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="03d62-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="03d62-199">Wyłącz stronę</span><span class="sxs-lookup"><span data-stu-id="03d62-199">Disable a page</span></span>

<span data-ttu-id="03d62-200">W tych sekcjach pokazano, jak wyłączyć stronę rejestracji, ale podejście może być używane do wyłączania dowolnej strony.</span><span class="sxs-lookup"><span data-stu-id="03d62-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="03d62-201">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03d62-201">To disable user registration:</span></span>

* <span data-ttu-id="03d62-202">Szkielet Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-202">Scaffold Identity.</span></span> <span data-ttu-id="03d62-203">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="03d62-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="03d62-204">Przykład:</span><span class="sxs-lookup"><span data-stu-id="03d62-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="03d62-205">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="03d62-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="03d62-206">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="03d62-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="03d62-207">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="03d62-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="03d62-208">Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="03d62-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="03d62-209">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="03d62-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="03d62-210">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="03d62-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="03d62-211">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="03d62-211">Use another app to add users</span></span>

<span data-ttu-id="03d62-212">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="03d62-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="03d62-213">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="03d62-213">Options to add users include:</span></span>

* <span data-ttu-id="03d62-214">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="03d62-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="03d62-215">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="03d62-215">A console app.</span></span>

<span data-ttu-id="03d62-216">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="03d62-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="03d62-217">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="03d62-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="03d62-218">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="03d62-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="03d62-219">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="03d62-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="03d62-220">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="03d62-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="03d62-221">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03d62-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="03d62-222">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="03d62-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="03d62-223">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="03d62-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="03d62-224">Aby uniemożliwić publikowanie statycznych Identity zasobów w katalogu głównym sieci Web, zobacz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="03d62-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03d62-225">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="03d62-225">Additional resources</span></span>

* [<span data-ttu-id="03d62-226">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="03d62-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03d62-227">ASP.NET Core 2,1 i nowsze udostępniają [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="03d62-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="03d62-228">Aplikacje, które obejmują, Identity mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="03d62-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="03d62-229">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="03d62-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="03d62-230">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="03d62-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="03d62-231">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="03d62-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="03d62-232">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="03d62-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="03d62-233">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania Identity pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="03d62-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="03d62-234">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="03d62-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="03d62-235">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="03d62-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="03d62-236">W tym dokumencie opisano kroki niezbędne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="03d62-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="03d62-237">Po Identity uruchomieniu szkieletu tworzony jest plik *ScaffoldingReadme.txt* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="03d62-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="03d62-238">Plik *ScaffoldingReadme.txt* zawiera ogólne instrukcje dotyczące tego, co jest potrzebne do ukończenia Identity aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="03d62-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="03d62-239">Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="03d62-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="03d62-240">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="03d62-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="03d62-241">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="03d62-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="03d62-242">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="03d62-243">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="03d62-244">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="03d62-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="03d62-245">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="03d62-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="03d62-246">Tworzenie szkieletu Identity w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="03d62-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="03d62-247">Dodaj następujące wyróżnione wywołania do `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="03d62-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="03d62-248">Tworzenie szkieletu Identity w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="03d62-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="03d62-249">jest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-250">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="03d62-251">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="03d62-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="03d62-252">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="03d62-252">Enable authentication</span></span>

<span data-ttu-id="03d62-253">W `Configure` metodzie `Startup` klasy Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="03d62-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="03d62-254">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="03d62-254">Layout changes</span></span>

<span data-ttu-id="03d62-255">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="03d62-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="03d62-256">Tworzenie szkieletu Identity w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="03d62-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="03d62-257">Niektóre Identity Opcje są konfigurowane w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-258">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="03d62-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="03d62-259">Tworzenie szkieletu Identity w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="03d62-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="03d62-260">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="03d62-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="03d62-261">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="03d62-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="03d62-262">jest skonfigurowany w *obszarach/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="03d62-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="03d62-263">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="03d62-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="03d62-264">Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="03d62-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="03d62-265">Tworzenie szkieletu Identity w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="03d62-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="03d62-266">Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="03d62-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="03d62-267">Utwórz pełne Identity Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="03d62-267">Create full Identity UI source</span></span>

<span data-ttu-id="03d62-268">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom program Identity szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="03d62-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="03d62-269">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za pomocą Identity aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="03d62-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="03d62-270">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="03d62-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="03d62-271">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="03d62-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="03d62-272">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="03d62-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="03d62-273">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="03d62-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="03d62-274">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="03d62-274">Disable register page</span></span>

<span data-ttu-id="03d62-275">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03d62-275">To disable user registration:</span></span>

* <span data-ttu-id="03d62-276">Szkielet Identity .</span><span class="sxs-lookup"><span data-stu-id="03d62-276">Scaffold Identity.</span></span> <span data-ttu-id="03d62-277">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="03d62-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="03d62-278">Przykład:</span><span class="sxs-lookup"><span data-stu-id="03d62-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="03d62-279">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="03d62-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="03d62-280">Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="03d62-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="03d62-281">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="03d62-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="03d62-282">Zaktualizuj stronę *obszary/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="03d62-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="03d62-283">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="03d62-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="03d62-284">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="03d62-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="03d62-285">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="03d62-285">Use another app to add users</span></span>

<span data-ttu-id="03d62-286">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="03d62-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="03d62-287">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="03d62-287">Options to add users include:</span></span>

* <span data-ttu-id="03d62-288">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="03d62-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="03d62-289">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="03d62-289">A console app.</span></span>

<span data-ttu-id="03d62-290">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="03d62-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="03d62-291">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="03d62-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="03d62-292">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="03d62-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="03d62-293">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="03d62-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="03d62-294">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="03d62-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="03d62-295">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="03d62-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="03d62-296">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="03d62-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03d62-297">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="03d62-297">Additional resources</span></span>

* [<span data-ttu-id="03d62-298">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="03d62-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
