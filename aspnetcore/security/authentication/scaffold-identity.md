---
title: Tożsamość szkieletu w projektach ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zidentyfikować szkielet w projekcie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604730"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="12c4d-103">Tożsamość szkieletu w projektach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12c4d-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="12c4d-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="12c4d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12c4d-105">ASP.NET Core zapewnia [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="12c4d-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="12c4d-106">Aplikacje zawierające tożsamość mogą zastosować szkielet, aby wybiórczo dodać kod źródłowy zawarty w bibliotece klas Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="12c4d-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="12c4d-107">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="12c4d-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="12c4d-108">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="12c4d-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="12c4d-109">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w RCL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="12c4d-110">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="12c4d-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="12c4d-111">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania pakietu tożsamości RCL.</span><span class="sxs-lookup"><span data-stu-id="12c4d-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="12c4d-112">Istnieje możliwość wybrania kodu tożsamości do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="12c4d-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="12c4d-113">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="12c4d-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="12c4d-114">W tym dokumencie opisano kroki niezbędne do ukończenia aktualizacji tworzenia szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="12c4d-115">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="12c4d-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="12c4d-116">Sprawdź zmiany po uruchomieniu szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="12c4d-117">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń z tożsamością.</span><span class="sxs-lookup"><span data-stu-id="12c4d-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="12c4d-118">Podczas tworzenia szkieletów tożsamości usługi lub usługi nie są generowane.</span><span class="sxs-lookup"><span data-stu-id="12c4d-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="12c4d-119">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="12c4d-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="12c4d-120">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="12c4d-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="12c4d-121">Podczas tworzenia szkieletu tożsamości z nowym kontekstem danych w projekcie z istniejącymi kontami:</span><span class="sxs-lookup"><span data-stu-id="12c4d-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="12c4d-122">W `Startup.ConfigureServices`programie Usuń wywołania do:</span><span class="sxs-lookup"><span data-stu-id="12c4d-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="12c4d-123">Na przykład `AddDbContext` i `AddDefaultIdentity` są oznaczone komentarzem w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="12c4d-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="12c4d-124">Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/Identity/IdentityHostingStartup. cs*</span><span class="sxs-lookup"><span data-stu-id="12c4d-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="12c4d-125">Zazwyczaj aplikacje utworzone przy użyciu poszczególnych kont ***nie*** powinny tworzyć nowego kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="12c4d-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="12c4d-126">Tożsamość szkieletowa do pustego projektu</span><span class="sxs-lookup"><span data-stu-id="12c4d-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="12c4d-127">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="12c4d-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="12c4d-128">Tożsamość szkieletowa w projekcie Razor bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="12c4d-129">Tożsamość jest konfigurowana w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="12c4d-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="12c4d-131">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="12c4d-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="12c4d-132">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="12c4d-132">Enable authentication</span></span>

<span data-ttu-id="12c4d-133">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="12c4d-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="12c4d-134">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="12c4d-134">Layout changes</span></span>

<span data-ttu-id="12c4d-135">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="12c4d-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="12c4d-136">Tożsamość szkieletowa w projekcie Razor z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="12c4d-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="12c4d-137">Niektóre opcje tożsamości są konfigurowane w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-138">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="12c4d-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="12c4d-139">Tożsamość szkieletowa do projektu MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="12c4d-140">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="12c4d-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="12c4d-141">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="12c4d-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="12c4d-142">Tożsamość jest konfigurowana w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-143">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="12c4d-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="12c4d-144">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="12c4d-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="12c4d-145">Tożsamość szkieletowa do projektu MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="12c4d-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="12c4d-146">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="12c4d-146">Create full identity UI source</span></span>

<span data-ttu-id="12c4d-147">Aby zachować pełną kontrolę nad interfejsem użytkownika tożsamości, uruchom szkielet tożsamości i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="12c4d-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="12c4d-148">Poniższy wyróżniony kod przedstawia zmiany w celu zastąpienia domyślnego interfejsu użytkownika tożsamości tożsamością w aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="12c4d-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="12c4d-149">Można to zrobić, aby mieć pełną kontrolę nad interfejsem użytkownika tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="12c4d-150">Domyślna tożsamość jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="12c4d-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="12c4d-151">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="12c4d-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="12c4d-152">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="12c4d-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="12c4d-153">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="12c4d-153">Disable register page</span></span>

<span data-ttu-id="12c4d-154">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="12c4d-154">To disable user registration:</span></span>

* <span data-ttu-id="12c4d-155">Tożsamość szkieletu.</span><span class="sxs-lookup"><span data-stu-id="12c4d-155">Scaffold Identity.</span></span> <span data-ttu-id="12c4d-156">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="12c4d-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="12c4d-157">Przykład:</span><span class="sxs-lookup"><span data-stu-id="12c4d-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="12c4d-158">Aktualizowanie *obszarów/tożsamości/stron/konta/register. cshtml. cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="12c4d-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="12c4d-159">Zaktualizuj *obszary/tożsamość/strony/account/register. cshtml* , aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="12c4d-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="12c4d-160">Skomentuj lub Usuń link rejestracji z *obszarów/tożsamości/stron/konta/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="12c4d-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="12c4d-161">Zaktualizuj stronę *obszary/tożsamość/strony/konto/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="12c4d-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="12c4d-162">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="12c4d-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="12c4d-163">Usuń kod potwierdzający z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="12c4d-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="12c4d-164">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-164">Use another app to add users</span></span>

<span data-ttu-id="12c4d-165">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="12c4d-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="12c4d-166">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="12c4d-166">Options to add users include:</span></span>

* <span data-ttu-id="12c4d-167">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="12c4d-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="12c4d-168">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="12c4d-168">A console app.</span></span>

<span data-ttu-id="12c4d-169">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="12c4d-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="12c4d-170">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="12c4d-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="12c4d-171">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="12c4d-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="12c4d-172">Użytkownik zostanie dodany do bazy danych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="12c4d-173">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="12c4d-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="12c4d-174">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="12c4d-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="12c4d-175">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="12c4d-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="12c4d-176">Zapobiegaj publikowaniu zasobów tożsamości statycznej</span><span class="sxs-lookup"><span data-stu-id="12c4d-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="12c4d-177">Aby zapobiec publikowaniu zasobów tożsamości statycznej do katalogu głównego sieci <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>Web, zobacz.</span><span class="sxs-lookup"><span data-stu-id="12c4d-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12c4d-178">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="12c4d-178">Additional resources</span></span>

* [<span data-ttu-id="12c4d-179">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="12c4d-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12c4d-180">ASP.NET Core 2,1 i nowsze zapewniają [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [Biblioteka klas Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="12c4d-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="12c4d-181">Aplikacje zawierające tożsamość mogą zastosować szkielet, aby wybiórczo dodać kod źródłowy zawarty w bibliotece klas Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="12c4d-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="12c4d-182">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="12c4d-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="12c4d-183">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="12c4d-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="12c4d-184">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w RCL tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="12c4d-185">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="12c4d-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="12c4d-186">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania pakietu tożsamości RCL.</span><span class="sxs-lookup"><span data-stu-id="12c4d-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="12c4d-187">Istnieje możliwość wybrania kodu tożsamości do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="12c4d-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="12c4d-188">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="12c4d-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="12c4d-189">W tym dokumencie opisano kroki niezbędne do ukończenia aktualizacji tworzenia szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="12c4d-190">Po uruchomieniu szkieletu tożsamości jest tworzony plik *ScaffoldingReadme. txt* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="12c4d-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="12c4d-191">Plik *ScaffoldingReadme. txt* zawiera ogólne instrukcje dotyczące tego, co jest potrzebne do ukończenia aktualizacji tworzenia szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="12c4d-192">Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="12c4d-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="12c4d-193">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="12c4d-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="12c4d-194">Sprawdź zmiany po uruchomieniu szkieletu tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="12c4d-195">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji zabezpieczeń z tożsamością.</span><span class="sxs-lookup"><span data-stu-id="12c4d-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="12c4d-196">Podczas tworzenia szkieletów tożsamości usługi lub usługi nie są generowane.</span><span class="sxs-lookup"><span data-stu-id="12c4d-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="12c4d-197">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="12c4d-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="12c4d-198">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="12c4d-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="12c4d-199">Tożsamość szkieletowa do pustego projektu</span><span class="sxs-lookup"><span data-stu-id="12c4d-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="12c4d-200">Dodaj następujące wyróżnione wywołania do `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="12c4d-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="12c4d-201">Tożsamość szkieletowa w projekcie Razor bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="12c4d-202">Tożsamość jest konfigurowana w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-203">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="12c4d-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="12c4d-204">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="12c4d-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="12c4d-205">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="12c4d-205">Enable authentication</span></span>

<span data-ttu-id="12c4d-206">W `Configure` `Startup` metodzie klasy Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="12c4d-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="12c4d-207">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="12c4d-207">Layout changes</span></span>

<span data-ttu-id="12c4d-208">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="12c4d-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="12c4d-209">Tożsamość szkieletowa w projekcie Razor z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="12c4d-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="12c4d-210">Niektóre opcje tożsamości są konfigurowane w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-211">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="12c4d-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="12c4d-212">Tożsamość szkieletowa do projektu MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="12c4d-213">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="12c4d-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="12c4d-214">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="12c4d-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="12c4d-215">Tożsamość jest konfigurowana w *obszarach/Identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="12c4d-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="12c4d-216">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="12c4d-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="12c4d-217">Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="12c4d-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="12c4d-218">Tożsamość szkieletowa do projektu MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="12c4d-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="12c4d-219">Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="12c4d-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="12c4d-220">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="12c4d-220">Create full identity UI source</span></span>

<span data-ttu-id="12c4d-221">Aby zachować pełną kontrolę nad interfejsem użytkownika tożsamości, uruchom szkielet tożsamości i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="12c4d-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="12c4d-222">Poniższy wyróżniony kod przedstawia zmiany w celu zastąpienia domyślnego interfejsu użytkownika tożsamości tożsamością w aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="12c4d-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="12c4d-223">Można to zrobić, aby mieć pełną kontrolę nad interfejsem użytkownika tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="12c4d-224">Domyślna tożsamość jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="12c4d-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="12c4d-225">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="12c4d-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="12c4d-226">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="12c4d-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="12c4d-227">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="12c4d-227">Disable register page</span></span>

<span data-ttu-id="12c4d-228">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="12c4d-228">To disable user registration:</span></span>

* <span data-ttu-id="12c4d-229">Tożsamość szkieletu.</span><span class="sxs-lookup"><span data-stu-id="12c4d-229">Scaffold Identity.</span></span> <span data-ttu-id="12c4d-230">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="12c4d-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="12c4d-231">Przykład:</span><span class="sxs-lookup"><span data-stu-id="12c4d-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="12c4d-232">Aktualizowanie *obszarów/tożsamości/stron/konta/register. cshtml. cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="12c4d-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="12c4d-233">Zaktualizuj *obszary/tożsamość/strony/account/register. cshtml* , aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="12c4d-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="12c4d-234">Skomentuj lub Usuń link rejestracji z *obszarów/tożsamości/stron/konta/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="12c4d-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="12c4d-235">Zaktualizuj stronę *obszary/tożsamość/strony/konto/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="12c4d-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="12c4d-236">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="12c4d-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="12c4d-237">Usuń kod potwierdzający z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="12c4d-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="12c4d-238">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="12c4d-238">Use another app to add users</span></span>

<span data-ttu-id="12c4d-239">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="12c4d-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="12c4d-240">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="12c4d-240">Options to add users include:</span></span>

* <span data-ttu-id="12c4d-241">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="12c4d-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="12c4d-242">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="12c4d-242">A console app.</span></span>

<span data-ttu-id="12c4d-243">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="12c4d-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="12c4d-244">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="12c4d-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="12c4d-245">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="12c4d-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="12c4d-246">Użytkownik zostanie dodany do bazy danych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="12c4d-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="12c4d-247">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="12c4d-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="12c4d-248">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="12c4d-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="12c4d-249">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="12c4d-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12c4d-250">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="12c4d-250">Additional resources</span></span>

* [<span data-ttu-id="12c4d-251">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="12c4d-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end