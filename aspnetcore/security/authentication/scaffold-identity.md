---
title: Szkielet Identity w projektach ASP.NET Core
author: rick-anderson
description: Dowiedz się, Identity jak szkieletować w projekcie ASP.NET Core.
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
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768393"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="3d14f-103">Szkielet Identity w projektach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d14f-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="3d14f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3d14f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3d14f-105">ASP.NET Core zapewnia [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3d14f-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3d14f-106">Aplikacje, które Identity obejmują, mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="3d14f-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3d14f-107">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3d14f-108">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="3d14f-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3d14f-109">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="3d14f-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3d14f-110">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="3d14f-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3d14f-111">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania pakietu Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="3d14f-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3d14f-112">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="3d14f-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3d14f-113">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="3d14f-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="3d14f-114">W tym dokumencie opisano kroki niezbędne do ukończenia aktualizacji Identity tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3d14f-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3d14f-115">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="3d14f-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3d14f-116">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="3d14f-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="3d14f-117">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji Identityzabezpieczeń w programie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3d14f-118">Usługi lub przecinki usług nie są generowane podczas Identitytworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3d14f-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3d14f-119">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3d14f-120">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3d14f-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="3d14f-121">Podczas tworzenia szkieletu Identity z nowym kontekstem danych w projekcie z istniejącymi kontami:</span><span class="sxs-lookup"><span data-stu-id="3d14f-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="3d14f-122">W `Startup.ConfigureServices`programie Usuń wywołania do:</span><span class="sxs-lookup"><span data-stu-id="3d14f-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="3d14f-123">Na przykład `AddDbContext` i `AddDefaultIdentity` są oznaczone komentarzem w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3d14f-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="3d14f-124">Poprzedzający kod oznacza kod, który jest duplikowany w *obszarach/Identity/IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="3d14f-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="3d14f-125">Zazwyczaj aplikacje utworzone przy użyciu poszczególnych kont ***nie*** powinny tworzyć nowego kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="3d14f-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3d14f-126">Tożsamość szkieletowa do pustego projektu</span><span class="sxs-lookup"><span data-stu-id="3d14f-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3d14f-127">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="3d14f-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3d14f-128">Tożsamość szkieletowa w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="3d14f-129">jest skonfigurowany w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-130">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3d14f-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3d14f-131">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="3d14f-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3d14f-132">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="3d14f-132">Enable authentication</span></span>

<span data-ttu-id="3d14f-133">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="3d14f-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3d14f-134">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="3d14f-134">Layout changes</span></span>

<span data-ttu-id="3d14f-135">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="3d14f-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3d14f-136">Tożsamość szkieletowa w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="3d14f-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="3d14f-137">Niektóre Identity opcje są konfigurowane w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-138">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3d14f-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3d14f-139">Tożsamość szkieletowa do projektu MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3d14f-140">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3d14f-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="3d14f-141">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3d14f-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="3d14f-142">jest skonfigurowany w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-143">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3d14f-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3d14f-144">Zaktualizuj `Startup` klasę przy użyciu kodu podobnego do poniższego:</span><span class="sxs-lookup"><span data-stu-id="3d14f-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3d14f-145">Tożsamość szkieletowa do projektu MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="3d14f-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3d14f-146">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="3d14f-146">Create full identity UI source</span></span>

<span data-ttu-id="3d14f-147">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom Identity program szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="3d14f-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3d14f-148">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za Identity pomocą aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3d14f-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3d14f-149">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3d14f-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3d14f-150">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3d14f-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3d14f-151">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3d14f-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3d14f-152">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="3d14f-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="3d14f-153">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="3d14f-153">Disable register page</span></span>

<span data-ttu-id="3d14f-154">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="3d14f-154">To disable user registration:</span></span>

* <span data-ttu-id="3d14f-155">Szkielet Identity.</span><span class="sxs-lookup"><span data-stu-id="3d14f-155">Scaffold Identity.</span></span> <span data-ttu-id="3d14f-156">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="3d14f-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3d14f-157">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3d14f-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3d14f-158">Zaktualizuj *obszary/Identity/Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="3d14f-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3d14f-159">Zaktualizuj *obszary/Identity/Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="3d14f-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3d14f-160">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarówIdentity//Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3d14f-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3d14f-161">Zaktualizuj stronę *obszary/Identity/Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="3d14f-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3d14f-162">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="3d14f-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3d14f-163">Usuń kod potwierdzający z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3d14f-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3d14f-164">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-164">Use another app to add users</span></span>

<span data-ttu-id="3d14f-165">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3d14f-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3d14f-166">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="3d14f-166">Options to add users include:</span></span>

* <span data-ttu-id="3d14f-167">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="3d14f-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="3d14f-168">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="3d14f-168">A console app.</span></span>

<span data-ttu-id="3d14f-169">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="3d14f-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3d14f-170">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3d14f-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="3d14f-171">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="3d14f-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3d14f-172">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3d14f-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3d14f-173">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="3d14f-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3d14f-174">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="3d14f-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3d14f-175">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="3d14f-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="3d14f-176">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="3d14f-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="3d14f-177">Aby uniemożliwić publikowanie statycznych Identity zasobów w katalogu głównym sieci Web, <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="3d14f-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d14f-178">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3d14f-178">Additional resources</span></span>

* [<span data-ttu-id="3d14f-179">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="3d14f-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3d14f-180">ASP.NET Core 2,1 i nowsze udostępniają [ Identity ASP.NET Core](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="3d14f-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3d14f-181">Aplikacje, które Identity obejmują, mogą zastosować szkieleter, aby selektywnie dodać kod źródłowy znajdujący się w Identity Razor bibliotece klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="3d14f-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3d14f-182">Może być konieczne wygenerowanie kodu źródłowego, aby można było zmodyfikować kod i zmienić zachowanie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3d14f-183">Na przykład możesz poinstruować szkielet, aby wygenerował kod używany w rejestracji.</span><span class="sxs-lookup"><span data-stu-id="3d14f-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3d14f-184">Wygenerowany kod ma pierwszeństwo przed tym samym kodem w Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="3d14f-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3d14f-185">Aby uzyskać pełną kontrolę nad interfejsem użytkownika i nie używać domyślnego RCL, zobacz sekcję [Tworzenie źródła interfejsu użytkownika pełnej tożsamości](#full).</span><span class="sxs-lookup"><span data-stu-id="3d14f-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3d14f-186">Aplikacje, które **nie** obejmują uwierzytelniania, mogą zastosować szkieleter w celu dodania pakietu Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="3d14f-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3d14f-187">Dostępna jest opcja wybierania Identity kodu do wygenerowania.</span><span class="sxs-lookup"><span data-stu-id="3d14f-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3d14f-188">Chociaż szkielet generuje większość niezbędnego kodu, należy zaktualizować projekt, aby ukończyć proces.</span><span class="sxs-lookup"><span data-stu-id="3d14f-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="3d14f-189">W tym dokumencie opisano kroki niezbędne do ukończenia aktualizacji Identity tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3d14f-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3d14f-190">Po uruchomieniu Identity szkieletu tworzony jest plik *ScaffoldingReadme. txt* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="3d14f-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="3d14f-191">Plik *ScaffoldingReadme. txt* zawiera ogólne instrukcje dotyczące tego, Identity co jest potrzebne do ukończenia aktualizacji tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3d14f-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="3d14f-192">Ten dokument zawiera pełniejsze instrukcje niż plik *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="3d14f-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="3d14f-193">Zalecamy używanie systemu kontroli źródła, który pokazuje różnice plików i pozwala na wycofanie zmian.</span><span class="sxs-lookup"><span data-stu-id="3d14f-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3d14f-194">Sprawdź zmiany po uruchomieniu Identity szkieletu.</span><span class="sxs-lookup"><span data-stu-id="3d14f-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="3d14f-195">Usługi są wymagane w przypadku korzystania z [uwierzytelniania dwuskładnikowego](xref:security/authentication/identity-enable-qrcodes), [potwierdzenia konta i odzyskiwania hasła](xref:security/authentication/accconfirm)oraz innych funkcji Identityzabezpieczeń w programie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3d14f-196">Usługi lub przecinki usług nie są generowane podczas Identitytworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3d14f-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3d14f-197">Usługi umożliwiające włączenie tych funkcji należy dodać ręcznie.</span><span class="sxs-lookup"><span data-stu-id="3d14f-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3d14f-198">Na przykład zapoznaj się z tematem [Żądaj potwierdzenia wiadomości e-mail](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="3d14f-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3d14f-199">Tożsamość szkieletowa do pustego projektu</span><span class="sxs-lookup"><span data-stu-id="3d14f-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3d14f-200">Dodaj następujące wyróżnione wywołania do `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="3d14f-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3d14f-201">Tożsamość szkieletowa w Razor projekcie bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="3d14f-202">jest skonfigurowany w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-203">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3d14f-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3d14f-204">Migracje, UseAuthentication i układ</span><span class="sxs-lookup"><span data-stu-id="3d14f-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3d14f-205">Włącz uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="3d14f-205">Enable authentication</span></span>

<span data-ttu-id="3d14f-206">W `Configure` `Startup` metodzie klasy Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="3d14f-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3d14f-207">Zmiany układu</span><span class="sxs-lookup"><span data-stu-id="3d14f-207">Layout changes</span></span>

<span data-ttu-id="3d14f-208">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku układu:</span><span class="sxs-lookup"><span data-stu-id="3d14f-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3d14f-209">Tożsamość szkieletowa w Razor projekcie z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="3d14f-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="3d14f-210">Niektóre Identity opcje są konfigurowane w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-211">Aby uzyskać więcej informacji, zobacz [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3d14f-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3d14f-212">Tożsamość szkieletowa do projektu MVC bez istniejącej autoryzacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3d14f-213">Opcjonalnie: Dodaj część logowania (`_LoginPartial`) do pliku *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3d14f-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="3d14f-214">Przenieś plik *Pages/Shared/_LoginPartial. cshtml* do *widoków/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3d14f-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="3d14f-215">jest skonfigurowany w *obszarach/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3d14f-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3d14f-216">Aby uzyskać więcej informacji, zobacz IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="3d14f-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3d14f-217">Wywołaj [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="3d14f-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3d14f-218">Tożsamość szkieletowa do projektu MVC z autoryzacją</span><span class="sxs-lookup"><span data-stu-id="3d14f-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3d14f-219">Usuwanie *stron/folderów udostępnionych* i plików w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="3d14f-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3d14f-220">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="3d14f-220">Create full identity UI source</span></span>

<span data-ttu-id="3d14f-221">Aby zachować pełną kontrolę nad Identity interfejsem użytkownika, uruchom Identity program szkieletowy i wybierz opcję **Zastąp wszystkie pliki**.</span><span class="sxs-lookup"><span data-stu-id="3d14f-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3d14f-222">Poniższy wyróżniony kod pokazuje zmiany w celu zastąpienia domyślnego Identity interfejsu użytkownika za Identity pomocą aplikacji internetowej ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="3d14f-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3d14f-223">Można to zrobić, aby mieć pełną kontrolę nad Identity interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3d14f-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3d14f-224">Wartość domyślna Identity jest zastępowana w następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3d14f-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3d14f-225">Poniższy kod ustawia [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)i [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="3d14f-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3d14f-226">Zarejestruj `IEmailSender` implementację, na przykład:</span><span class="sxs-lookup"><span data-stu-id="3d14f-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="3d14f-227">Wyłącz stronę rejestracji</span><span class="sxs-lookup"><span data-stu-id="3d14f-227">Disable register page</span></span>

<span data-ttu-id="3d14f-228">Aby wyłączyć rejestrację użytkownika:</span><span class="sxs-lookup"><span data-stu-id="3d14f-228">To disable user registration:</span></span>

* <span data-ttu-id="3d14f-229">Szkielet Identity.</span><span class="sxs-lookup"><span data-stu-id="3d14f-229">Scaffold Identity.</span></span> <span data-ttu-id="3d14f-230">Uwzględnij konto. Register, Account. login i Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="3d14f-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3d14f-231">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3d14f-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3d14f-232">Zaktualizuj *obszary/Identity/Pages/Account/register.cshtml.cs* , aby użytkownicy nie mogli zarejestrować się z tego punktu końcowego:</span><span class="sxs-lookup"><span data-stu-id="3d14f-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3d14f-233">Zaktualizuj *obszary/Identity/Pages/Account/register.cshtml* tak, aby były zgodne z poprzednimi zmianami:</span><span class="sxs-lookup"><span data-stu-id="3d14f-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3d14f-234">Dodawanie komentarza lub usuwanie linku rejestracji z *obszarówIdentity//Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3d14f-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3d14f-235">Zaktualizuj stronę *obszary/Identity/Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="3d14f-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3d14f-236">Usuń kod i linki z pliku cshtml.</span><span class="sxs-lookup"><span data-stu-id="3d14f-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3d14f-237">Usuń kod potwierdzający z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="3d14f-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3d14f-238">Dodawanie użytkowników przy użyciu innej aplikacji</span><span class="sxs-lookup"><span data-stu-id="3d14f-238">Use another app to add users</span></span>

<span data-ttu-id="3d14f-239">Zapewnianie mechanizmu dodawania użytkowników spoza aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3d14f-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3d14f-240">Dostępne są następujące opcje dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="3d14f-240">Options to add users include:</span></span>

* <span data-ttu-id="3d14f-241">Dedykowana aplikacja sieci Web administratora.</span><span class="sxs-lookup"><span data-stu-id="3d14f-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="3d14f-242">Aplikacja konsolowa.</span><span class="sxs-lookup"><span data-stu-id="3d14f-242">A console app.</span></span>

<span data-ttu-id="3d14f-243">Poniższy kod przedstawia jedno podejście do dodawania użytkowników:</span><span class="sxs-lookup"><span data-stu-id="3d14f-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3d14f-244">Lista użytkowników jest odczytywana w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3d14f-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="3d14f-245">Dla każdego użytkownika jest generowany silny unikatowy hasło.</span><span class="sxs-lookup"><span data-stu-id="3d14f-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3d14f-246">Użytkownik zostanie dodany do Identity bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3d14f-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3d14f-247">Użytkownik zostanie powiadomiony i zostanie poinformowany o zmianie hasła.</span><span class="sxs-lookup"><span data-stu-id="3d14f-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3d14f-248">Poniższy kod zawiera opis dodawania użytkownika:</span><span class="sxs-lookup"><span data-stu-id="3d14f-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3d14f-249">Podobne podejście może być stosowane w scenariuszach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="3d14f-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d14f-250">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3d14f-250">Additional resources</span></span>

* [<span data-ttu-id="3d14f-251">Zmiany w kodzie uwierzytelniania na ASP.NET Core 2,1 i nowsze</span><span class="sxs-lookup"><span data-stu-id="3d14f-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end