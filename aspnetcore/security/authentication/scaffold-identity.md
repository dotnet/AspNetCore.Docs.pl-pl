---
title: 'Szkielet :::no-loc(Identity)::: w projektach ASP.NET Core'
author: rick-anderson
description: 'Dowiedz się, jak szkieletować :::no-loc(Identity)::: w projekcie ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053322"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="92dd1-103">Szkielet :::no-loc(Identity)::: w projektach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92dd1-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="92dd1-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92dd1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92dd1-105">ASP.NET Core udostępnia [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) jako [ :::no-loc(Razor)::: Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="92dd1-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="92dd1-106">Aplikacje, które obejmują, :::no-loc(Identity)::: mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w :::no-loc(Identity)::: :::no-loc(Razor)::: bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="92dd1-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="92dd1-107">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="92dd1-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="92dd1-108">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="92dd1-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="92dd1-109">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="92dd1-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="92dd1-110">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać RCL domyślnego, zobacz sekcję [Tworzenie pełnego :::no-loc(Identity)::: źródła interfejsu użytkownika](#full).</span><span class="sxs-lookup"><span data-stu-id="92dd1-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="92dd1-111">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania :::no-loc(Identity)::: pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="92dd1-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="92dd1-112">Dostępna jest opcja wybierania :::no-loc(Identity)::: kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="92dd1-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="92dd1-113">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="92dd1-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="92dd1-114">W tym dokumencie opisano kroki niezbędne do ukończenia :::no-loc(Identity)::: aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="92dd1-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="92dd1-115">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="92dd1-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="92dd1-116">Sprawdź zmiany po uruchomieniu :::no-loc(Identity)::: szkieletu.</span><span class="sxs-lookup"><span data-stu-id="92dd1-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="92dd1-117">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-118">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-119">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="92dd1-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="92dd1-120">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="92dd1-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="92dd1-121">Podczas tworzenia szkieletu :::no-loc(Identity)::: z nowym kontekstem danych w projekcie z istniejącymi kontami:</span><span class="sxs-lookup"><span data-stu-id="92dd1-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="92dd1-122">W programie `Startup.ConfigureServices` Usuń wywołania do:</span><span class="sxs-lookup"><span data-stu-id="92dd1-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="92dd1-123">Na przykład `AddDbContext` i `AddDefault:::no-loc(Identity):::` są oznaczone komentarzem w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="92dd1-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="92dd1-124">Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="92dd1-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="92dd1-125">Zazwyczaj aplikacje, które zostały utworzone przy użyciu pojedynczych kont, powinny \* **nie** _ utworzyć nowego kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="92dd1-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="92dd1-126">Tworzenie szkieletu :::no-loc(Identity)::: w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="92dd1-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-127">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="92dd1-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="92dd1-128">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Razor)::: projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-129">:::no-loc(Identity):::jest skonfigurowany w _Areas/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs \*.</span><span class="sxs-lookup"><span data-stu-id="92dd1-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="92dd1-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="92dd1-131">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="92dd1-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="92dd1-132">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="92dd1-132">Enable authentication</span></span>

<span data-ttu-id="92dd1-133">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="92dd1-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="92dd1-134">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="92dd1-134">Layout changes</span></span>

<span data-ttu-id="92dd1-135">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="92dd1-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="92dd1-136">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Razor)::: projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="92dd1-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="92dd1-137">Niektóre :::no-loc(Identity)::: Opcje są konfigurowane w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-138">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="92dd1-139">Tworzenie szkieletu :::no-loc(Identity)::: w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-140">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="92dd1-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="92dd1-141">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="92dd1-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="92dd1-142">:::no-loc(Identity):::jest skonfigurowany w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-143">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="92dd1-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="92dd1-144">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="92dd1-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="92dd1-145">Tworzenie szkieletu :::no-loc(Identity)::: w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="92dd1-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="92dd1-146">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Blazor Server)::: projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-147">:::no-loc(Identity):::jest skonfigurowany w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-148">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="92dd1-149">Migracje</span><span class="sxs-lookup"><span data-stu-id="92dd1-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="92dd1-150">Przekaż token XSRF do aplikacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="92dd1-151">Tokeny mogą być przesyłane do składników:</span><span class="sxs-lookup"><span data-stu-id="92dd1-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="92dd1-152">Gdy tokeny uwierzytelniania są inicjowane i zapisywane w ramach uwierzytelniania :::no-loc(cookie)::: , można je przekazywać do składników programu.</span><span class="sxs-lookup"><span data-stu-id="92dd1-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="92dd1-153">:::no-loc(Razor)::: składniki nie mogą być używane `HttpContext` bezpośrednio, dlatego nie ma możliwości uzyskania [tokenu fałszerstwa (XSRF)](xref:security/anti-request-forgery) w celu opublikowania w :::no-loc(Identity)::: punkcie końcowym wylogowywania `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="92dd1-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="92dd1-154">Token XSRF można przesłać do składników.</span><span class="sxs-lookup"><span data-stu-id="92dd1-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="92dd1-155">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="92dd1-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="92dd1-156">W pliku *Pages/_Host. cshtml* Ustanów token po dodaniu go do `InitialApplicationState` `TokenProvider` klas i:</span><span class="sxs-lookup"><span data-stu-id="92dd1-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="92dd1-157">Zaktualizuj `App` składnik ( *App. Razor* ), aby przypisać `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="92dd1-158">Usługa przedstawiona `TokenProvider` w temacie jest używana w `LoginDisplay` składniku w poniższej sekcji [zmiany układu i przepływu uwierzytelniania](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="92dd1-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="92dd1-159">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="92dd1-159">Enable authentication</span></span>

<span data-ttu-id="92dd1-160">W `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="92dd1-160">In the `Startup` class:</span></span>

* <span data-ttu-id="92dd1-161">Potwierdź, że :::no-loc(Razor)::: usługi stron są dodawane w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="92dd1-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="92dd1-162">W przypadku korzystania z [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)Zarejestruj usługę.</span><span class="sxs-lookup"><span data-stu-id="92dd1-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="92dd1-163">Zadzwoń do programu `UseDatabaseErrorPage` Application Builder w `Startup.Configure` środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="92dd1-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="92dd1-164">Wywołanie `UseAuthentication` i `UseAuthorization` po `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="92dd1-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="92dd1-165">Dodaj punkt końcowy dla :::no-loc(Razor)::: stron.</span><span class="sxs-lookup"><span data-stu-id="92dd1-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="92dd1-166">Zmiany przepływu układu i uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="92dd1-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="92dd1-167">Dodaj `RedirectToLogin` składnik ( *RedirectToLogin. Razor* ) do folderu *udostępnionego* aplikacji w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="92dd1-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Dodaj `LoginDisplay` składnik ( *LoginDisplay. Razor* ) do folderu *udostępnionego* aplikacji. <span data-ttu-id="92dd1-169">[Usługa TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) udostępnia token XSRF formularza HTML, który jest wysyłany do :::no-loc(Identity)::: punktu końcowego wylogowania:</span><span class="sxs-lookup"><span data-stu-id="92dd1-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="92dd1-170">W `MainLayout` składniku ( *Shared/MainLayout. Razor* ) Dodaj `LoginDisplay` składnik do zawartości elementu najwyższego wiersza `<div>` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="92dd1-171">Punkty końcowe uwierzytelniania stylu</span><span class="sxs-lookup"><span data-stu-id="92dd1-171">Style authentication endpoints</span></span>

<span data-ttu-id="92dd1-172">Ponieważ :::no-loc(Blazor Server)::: :::no-loc(Razor)::: program używa :::no-loc(Identity)::: stron stron, style interfejsu użytkownika zmieniają się, gdy użytkownik przechodzi między :::no-loc(Identity)::: stronami i składnikami.</span><span class="sxs-lookup"><span data-stu-id="92dd1-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="92dd1-173">Dostępne są dwie opcje umożliwiające zaadresowanie stylów Incongruous:</span><span class="sxs-lookup"><span data-stu-id="92dd1-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="92dd1-174">:::no-loc(Identity):::Składniki kompilacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="92dd1-175">Podejście do używania składników :::no-loc(Identity)::: zamiast stron polega na tworzeniu :::no-loc(Identity)::: składników.</span><span class="sxs-lookup"><span data-stu-id="92dd1-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="92dd1-176">Ponieważ `SignInManager` i `UserManager` nie są obsługiwane w :::no-loc(Razor)::: składnikach, użyj punktów końcowych interfejsu API w :::no-loc(Blazor Server)::: aplikacji, aby przetworzyć akcje konta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92dd1-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="92dd1-177">Używanie układu niestandardowego ze :::no-loc(Blazor)::: stylami aplikacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="92dd1-178">:::no-loc(Identity):::Układ stron i style można modyfikować, aby utworzyć strony używające :::no-loc(Blazor)::: motywu domyślnego.</span><span class="sxs-lookup"><span data-stu-id="92dd1-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="92dd1-179">Przykład w tej sekcji jest jedynie punktem wyjścia do dostosowania.</span><span class="sxs-lookup"><span data-stu-id="92dd1-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="92dd1-180">Dodatkowe czynności są prawdopodobnie wymagane do osiągnięcia najlepszego środowiska użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92dd1-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="92dd1-181">Utwórz nowy `NavMenu_:::no-loc(Identity):::Layout` składnik ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="92dd1-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="92dd1-182">W przypadku znaczników i kodu składnika Użyj tej samej zawartości `NavMenu` składnika aplikacji ( *Shared/NavMenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="92dd1-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="92dd1-183">`NavLink`Przełączaj wszystkie elementy s do składników, których nie można uzyskać anonimowo, ponieważ Automatyczne przekierowania w `RedirectToLogin` składniku kończą się niepowodzeniem dla składników wymagających uwierzytelniania lub autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="92dd1-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="92dd1-184">W pliku *Pages/Shared/Layout. cshtml* wprowadź następujące zmiany:</span><span class="sxs-lookup"><span data-stu-id="92dd1-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="92dd1-185">Dodaj :::no-loc(Razor)::: dyrektywy na początku pliku, aby używać pomocników tagów i składników aplikacji w folderze *udostępnionym* :</span><span class="sxs-lookup"><span data-stu-id="92dd1-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="92dd1-186">Zamień `{APPLICATION ASSEMBLY}` na nazwę zestawu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="92dd1-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="92dd1-187">Dodaj `<base>` tag i :::no-loc(Blazor)::: arkusz stylów `<link>` do `<head>` zawartości:</span><span class="sxs-lookup"><span data-stu-id="92dd1-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="92dd1-188">Zmień zawartość `<body>` tagu na następującą:</span><span class="sxs-lookup"><span data-stu-id="92dd1-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="92dd1-189">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Blazor Server)::: projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="92dd1-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="92dd1-190">Niektóre :::no-loc(Identity)::: Opcje są konfigurowane w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-191">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="92dd1-192">Utwórz pełne :::no-loc(Identity)::: Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="92dd1-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="92dd1-193">Aby zachować pełną kontrolę nad :::no-loc(Identity)::: interfejsem użytkownika, uruchom program :::no-loc(Identity)::: szkieletowy i wybierz opcję **Zastąp wszystkie pliki** .</span><span class="sxs-lookup"><span data-stu-id="92dd1-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="92dd1-194">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego :::no-loc(Identity)::: interfejsu użytkownika za pomocą :::no-loc(Identity)::: aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="92dd1-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="92dd1-195">Można to zrobić, aby mieć pełną kontrolę nad :::no-loc(Identity)::: interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92dd1-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="92dd1-196">Wartość domyślna :::no-loc(Identity)::: jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="92dd1-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="92dd1-197">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="92dd1-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="92dd1-198">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="92dd1-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="92dd1-199">Konfiguracja hasła</span><span class="sxs-lookup"><span data-stu-id="92dd1-199">Password configuration</span></span>

<span data-ttu-id="92dd1-200">Jeśli <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="92dd1-201">`InputModel``Password`właściwości znajdują się w następujących plikach:</span><span class="sxs-lookup"><span data-stu-id="92dd1-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="92dd1-202">Wyłącz stronę</span><span class="sxs-lookup"><span data-stu-id="92dd1-202">Disable a page</span></span>

<span data-ttu-id="92dd1-203">W tych sekcjach pokazano, jak wyłączyć stronę rejestracji, ale podejście może być używane do wyłączania dowolnej strony.</span><span class="sxs-lookup"><span data-stu-id="92dd1-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="92dd1-204">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="92dd1-204">To disable user registration:</span></span>

* <span data-ttu-id="92dd1-205">Szkielet :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-206">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="92dd1-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="92dd1-207">Przykład:</span><span class="sxs-lookup"><span data-stu-id="92dd1-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="92dd1-208">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="92dd1-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="92dd1-209">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="92dd1-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="92dd1-210">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92dd1-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="92dd1-211">Zaktualizuj stronę *obszary/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="92dd1-212">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="92dd1-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="92dd1-213">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="92dd1-214">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-214">Use another app to add users</span></span>

<span data-ttu-id="92dd1-215">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="92dd1-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="92dd1-216">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="92dd1-216">Options to add users include:</span></span>

* <span data-ttu-id="92dd1-217">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="92dd1-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="92dd1-218">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="92dd1-218">A console app.</span></span>

<span data-ttu-id="92dd1-219">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="92dd1-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="92dd1-220">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="92dd1-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="92dd1-221">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="92dd1-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="92dd1-222">Użytkownik zostanie dodany do :::no-loc(Identity)::: bazy danych.</span><span class="sxs-lookup"><span data-stu-id="92dd1-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="92dd1-223">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="92dd1-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="92dd1-224">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="92dd1-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="92dd1-225">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="92dd1-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="92dd1-226">Zapobiegaj publikowaniu :::no-loc(Identity)::: zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="92dd1-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="92dd1-227">Aby uniemożliwić publikowanie statycznych :::no-loc(Identity)::: zasobów w katalogu głównym sieci Web, zobacz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="92dd1-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92dd1-228">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="92dd1-228">Additional resources</span></span>

* [<span data-ttu-id="92dd1-229">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="92dd1-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92dd1-230">ASP.NET Core 2,1 i nowszy stanowi [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) [ :::no-loc(Razor)::: bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="92dd1-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="92dd1-231">Aplikacje, które obejmują, :::no-loc(Identity)::: mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w :::no-loc(Identity)::: :::no-loc(Razor)::: bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="92dd1-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="92dd1-232">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="92dd1-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="92dd1-233">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="92dd1-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="92dd1-234">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w :::no-loc(Identity)::: RCL.</span><span class="sxs-lookup"><span data-stu-id="92dd1-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="92dd1-235">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="92dd1-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="92dd1-236">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania :::no-loc(Identity)::: pakietu RCL.</span><span class="sxs-lookup"><span data-stu-id="92dd1-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="92dd1-237">Dostępna jest opcja wybierania :::no-loc(Identity)::: kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="92dd1-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="92dd1-238">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="92dd1-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="92dd1-239">W tym dokumencie opisano kroki niezbędne do ukończenia :::no-loc(Identity)::: aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="92dd1-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="92dd1-240">Po :::no-loc(Identity)::: uruchomieniu szkieletu tworzony jest plik *ScaffoldingReadme.txt* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="92dd1-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="92dd1-241">Plik *ScaffoldingReadme.txt* zawiera ogólne instrukcje dotyczące tego, co jest potrzebne do ukończenia :::no-loc(Identity)::: aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="92dd1-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="92dd1-242">Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme.txt* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="92dd1-243">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="92dd1-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="92dd1-244">Sprawdź zmiany po uruchomieniu :::no-loc(Identity)::: szkieletu.</span><span class="sxs-lookup"><span data-stu-id="92dd1-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="92dd1-245">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń w programie :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-246">Usługi lub przecinki usług nie są generowane podczas tworzenia szkieletów :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-247">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="92dd1-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="92dd1-248">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="92dd1-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="92dd1-249">Tworzenie szkieletu :::no-loc(Identity)::: w pustym projekcie</span><span class="sxs-lookup"><span data-stu-id="92dd1-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-250">Dodaj następujące wyróżnione wywołania do `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="92dd1-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="92dd1-251">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Razor)::: projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-252">:::no-loc(Identity):::jest skonfigurowany w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-253">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="92dd1-254">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="92dd1-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="92dd1-255">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="92dd1-255">Enable authentication</span></span>

<span data-ttu-id="92dd1-256">W `Configure` metodzie `Startup` klasy Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="92dd1-257">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="92dd1-257">Layout changes</span></span>

<span data-ttu-id="92dd1-258">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="92dd1-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="92dd1-259">Tworzenie szkieletu :::no-loc(Identity)::: w :::no-loc(Razor)::: projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="92dd1-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="92dd1-260">Niektóre :::no-loc(Identity)::: Opcje są konfigurowane w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-261">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="92dd1-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="92dd1-262">Tworzenie szkieletu :::no-loc(Identity)::: w projekcie MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="92dd1-263">Opcjonalnie: Dodaj część logowania ( `_LoginPartial` ) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="92dd1-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="92dd1-264">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="92dd1-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="92dd1-265">:::no-loc(Identity):::jest skonfigurowany w *obszarach/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="92dd1-266">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="92dd1-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="92dd1-267">Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="92dd1-268">Tworzenie szkieletu :::no-loc(Identity)::: w projekcie MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="92dd1-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="92dd1-269">Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="92dd1-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="92dd1-270">Utwórz pełne :::no-loc(Identity)::: Źródło interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="92dd1-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="92dd1-271">Aby zachować pełną kontrolę nad :::no-loc(Identity)::: interfejsem użytkownika, uruchom program :::no-loc(Identity)::: szkieletowy i wybierz opcję **Zastąp wszystkie pliki** .</span><span class="sxs-lookup"><span data-stu-id="92dd1-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="92dd1-272">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego :::no-loc(Identity)::: interfejsu użytkownika za pomocą :::no-loc(Identity)::: aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="92dd1-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="92dd1-273">Można to zrobić, aby mieć pełną kontrolę nad :::no-loc(Identity)::: interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92dd1-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="92dd1-274">Wartość domyślna :::no-loc(Identity)::: jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="92dd1-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="92dd1-275">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="92dd1-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="92dd1-276">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="92dd1-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="92dd1-277">Konfiguracja hasła</span><span class="sxs-lookup"><span data-stu-id="92dd1-277">Password configuration</span></span>

<span data-ttu-id="92dd1-278">Jeśli <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="92dd1-279">`InputModel``Password`właściwości znajdują się w następujących plikach:</span><span class="sxs-lookup"><span data-stu-id="92dd1-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="92dd1-280">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="92dd1-280">Disable register page</span></span>

<span data-ttu-id="92dd1-281">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="92dd1-281">To disable user registration:</span></span>

* <span data-ttu-id="92dd1-282">Szkielet :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="92dd1-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="92dd1-283">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="92dd1-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="92dd1-284">Przykład:</span><span class="sxs-lookup"><span data-stu-id="92dd1-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="92dd1-285">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="92dd1-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="92dd1-286">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="92dd1-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="92dd1-287">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarów/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92dd1-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="92dd1-288">Zaktualizuj stronę *obszary/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="92dd1-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="92dd1-289">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="92dd1-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="92dd1-290">Usuń kod potwierdzający z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="92dd1-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="92dd1-291">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="92dd1-291">Use another app to add users</span></span>

<span data-ttu-id="92dd1-292">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="92dd1-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="92dd1-293">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="92dd1-293">Options to add users include:</span></span>

* <span data-ttu-id="92dd1-294">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="92dd1-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="92dd1-295">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="92dd1-295">A console app.</span></span>

<span data-ttu-id="92dd1-296">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="92dd1-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="92dd1-297">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="92dd1-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="92dd1-298">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="92dd1-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="92dd1-299">Użytkownik zostanie dodany do :::no-loc(Identity)::: bazy danych.</span><span class="sxs-lookup"><span data-stu-id="92dd1-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="92dd1-300">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="92dd1-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="92dd1-301">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="92dd1-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="92dd1-302">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="92dd1-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92dd1-303">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="92dd1-303">Additional resources</span></span>

* [<span data-ttu-id="92dd1-304">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="92dd1-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
