---
title: Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację. Obejmuje HTTPS, uwierzytelnianie, zabezpieczenia, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 7d4c10fa0b1c569179fc3e0a518917ec0185c51f
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160280"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="70d37-104">Tworzenie aplikacji sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="70d37-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="70d37-105">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="70d37-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="70d37-106">Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="70d37-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70d37-107">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="70d37-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="70d37-108">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="70d37-109">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="70d37-109">There are three security groups:</span></span>

* <span data-ttu-id="70d37-110">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="70d37-111">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="70d37-112">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="70d37-113">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="70d37-114">Obrazy w tym dokumencie nie są dokładnie zgodne z najnowszymi szablonami.</span><span class="sxs-lookup"><span data-stu-id="70d37-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="70d37-115">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="70d37-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="70d37-116">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="70d37-117">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="70d37-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="70d37-118">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="70d37-120">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="70d37-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="70d37-122">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="70d37-122">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="70d37-124">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="70d37-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="70d37-125">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="70d37-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="70d37-127">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="70d37-127">The administrator has all privileges.</span></span> <span data-ttu-id="70d37-128">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="70d37-129">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="70d37-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="70d37-130">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="70d37-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="70d37-131">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="70d37-132">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="70d37-133">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70d37-134">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="70d37-134">Prerequisites</span></span>

<span data-ttu-id="70d37-135">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="70d37-135">This tutorial is advanced.</span></span> <span data-ttu-id="70d37-136">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="70d37-136">You should be familiar with:</span></span>

* [<span data-ttu-id="70d37-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70d37-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="70d37-138">Uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="70d37-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="70d37-139">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="70d37-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="70d37-140">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="70d37-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="70d37-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="70d37-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="70d37-142">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="70d37-142">The starter and completed app</span></span>

<span data-ttu-id="70d37-143">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="70d37-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="70d37-144">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="70d37-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="70d37-145">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="70d37-145">The starter app</span></span>

<span data-ttu-id="70d37-146">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="70d37-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="70d37-147">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="70d37-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="70d37-148">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="70d37-148">Secure user data</span></span>

<span data-ttu-id="70d37-149">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="70d37-150">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="70d37-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="70d37-151">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="70d37-151">Tie the contact data to the user</span></span>

<span data-ttu-id="70d37-152">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="70d37-153">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="70d37-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="70d37-154">`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="70d37-155">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="70d37-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="70d37-156">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="70d37-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="70d37-157">Dodawanie usług ról do programuIdentity</span><span class="sxs-lookup"><span data-stu-id="70d37-157">Add Role services to Identity</span></span>

<span data-ttu-id="70d37-158">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="70d37-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="70d37-159">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="70d37-159">Require authenticated users</span></span>

<span data-ttu-id="70d37-160">Ustaw rezerwowe zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="70d37-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="70d37-161">Poprzedni wyróżniony kod ustawia [rezerwowe zasady uwierzytelniania](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="70d37-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="70d37-162">Rezerwowe zasady uwierzytelniania wymagają uwierzytelnienia ***wszystkich*** użytkowników, z wyjątkiem Razor stron, kontrolerów lub metod akcji z atrybutem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="70d37-162">The fallback authentication policy requires ***all*** users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="70d37-163">Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu uwierzytelniania zamiast rezerwowych zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="70d37-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="70d37-164">Rezerwowe zasady uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="70d37-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="70d37-165">Stosuje się do wszystkich żądań, które nie określają jawnie zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="70d37-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="70d37-166">W przypadku żądań obsłużonych przez Routing punktów końcowych może to obejmować wszystkie punkty końcowe, które nie określają atrybutu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="70d37-167">W przypadku żądań obsłużonych przez inne oprogramowanie pośredniczące po wyzwoleniu na oprogramowanie pośredniczące, takie jak [pliki statyczne](xref:fundamentals/static-files), zasady te będą stosowane do wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="70d37-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="70d37-168">Ustawienie rezerwowych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="70d37-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="70d37-169">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="70d37-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="70d37-170"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Klasa zawiera również <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="70d37-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="70d37-171">`DefaultPolicy`To zasady używane z `[Authorize]` atrybutem, jeśli nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="70d37-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="70d37-172">`[Authorize]`nie zawiera nazwanych zasad, w przeciwieństwie do `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="70d37-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="70d37-173">Aby uzyskać więcej informacji na temat zasad, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="70d37-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="70d37-174">Alternatywny sposób, w jaki kontrolery MVC i Razor strony wymagające uwierzytelnienia wszystkich użytkowników są dodawane filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="70d37-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="70d37-175">Poprzedni kod używa filtru autoryzacji, ustawienie zasad powrotu używa routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="70d37-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="70d37-176">Ustawienie zasad powrotu jest preferowanym sposobem wymagania uwierzytelniania wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="70d37-177">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do `Index` stron i, `Privacy` aby anonimowi użytkownicy mogli uzyskać informacje o witrynie przed zarejestrowaniem:</span><span class="sxs-lookup"><span data-stu-id="70d37-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="70d37-178">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="70d37-178">Configure the test account</span></span>

<span data-ttu-id="70d37-179">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="70d37-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="70d37-180">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="70d37-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="70d37-181">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="70d37-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="70d37-182">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="70d37-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="70d37-183">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="70d37-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="70d37-184">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="70d37-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="70d37-185">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="70d37-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="70d37-186">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="70d37-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="70d37-187">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="70d37-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="70d37-188">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="70d37-189">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="70d37-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="70d37-190">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="70d37-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="70d37-191">Utwórz `ContactIsOwnerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="70d37-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70d37-192">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="70d37-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="70d37-193">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="70d37-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="70d37-194">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="70d37-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="70d37-195">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="70d37-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="70d37-196">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="70d37-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="70d37-197">`Task.CompletedTask`nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="70d37-198">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="70d37-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="70d37-199">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="70d37-200">`ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="70d37-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="70d37-201">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="70d37-201">Create a manager authorization handler</span></span>

<span data-ttu-id="70d37-202">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="70d37-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70d37-203">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="70d37-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="70d37-204">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="70d37-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="70d37-205">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="70d37-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="70d37-206">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="70d37-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70d37-207">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="70d37-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="70d37-208">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="70d37-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="70d37-209">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="70d37-209">Register the authorization handlers</span></span>

<span data-ttu-id="70d37-210">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="70d37-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="70d37-211">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="70d37-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="70d37-212">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="70d37-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="70d37-213">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70d37-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="70d37-214">`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="70d37-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="70d37-215">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="70d37-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="70d37-216">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="70d37-216">Support authorization</span></span>

<span data-ttu-id="70d37-217">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="70d37-218">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="70d37-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="70d37-219">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="70d37-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="70d37-220">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="70d37-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="70d37-221">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="70d37-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="70d37-222">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="70d37-223">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="70d37-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="70d37-224">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="70d37-224">The preceding code:</span></span>

* <span data-ttu-id="70d37-225">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="70d37-226">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="70d37-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="70d37-227">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="70d37-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="70d37-228">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="70d37-228">Update the CreateModel</span></span>

<span data-ttu-id="70d37-229">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="70d37-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="70d37-230">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="70d37-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="70d37-231">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="70d37-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="70d37-232">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="70d37-233">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="70d37-233">Update the IndexModel</span></span>

<span data-ttu-id="70d37-234">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="70d37-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="70d37-235">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="70d37-235">Update the EditModel</span></span>

<span data-ttu-id="70d37-236">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="70d37-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="70d37-237">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="70d37-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="70d37-238">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="70d37-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="70d37-239">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="70d37-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="70d37-240">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="70d37-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="70d37-241">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="70d37-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="70d37-242">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="70d37-242">Update the DeleteModel</span></span>

<span data-ttu-id="70d37-243">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="70d37-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="70d37-244">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="70d37-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="70d37-245">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="70d37-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="70d37-246">Wsuń usługę autoryzacji w pliku *Pages/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="70d37-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="70d37-247">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="70d37-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="70d37-248">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="70d37-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="70d37-249">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="70d37-250">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="70d37-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="70d37-251">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="70d37-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="70d37-252">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="70d37-253">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="70d37-253">Update Details</span></span>

<span data-ttu-id="70d37-254">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="70d37-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="70d37-255">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="70d37-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="70d37-256">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="70d37-256">Add or remove a user to a role</span></span>

<span data-ttu-id="70d37-257">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="70d37-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="70d37-258">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-258">Removing privileges from a user.</span></span> <span data-ttu-id="70d37-259">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="70d37-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="70d37-260">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="70d37-261">Różnice między wyzwaniem i Zabroń</span><span class="sxs-lookup"><span data-stu-id="70d37-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="70d37-262">Ta aplikacja ustawia zasady domyślne, aby [wymagać uwierzytelnionych użytkowników](#rau).</span><span class="sxs-lookup"><span data-stu-id="70d37-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="70d37-263">Poniższy kod umożliwia anonimowym użytkownikom.</span><span class="sxs-lookup"><span data-stu-id="70d37-263">The following code allows anonymous users.</span></span> <span data-ttu-id="70d37-264">Użytkownicy anonimowi mogą wyświetlać różnice między wyzwaniem a zabranianiem.</span><span class="sxs-lookup"><span data-stu-id="70d37-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="70d37-265">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="70d37-265">In the preceding code:</span></span>

* <span data-ttu-id="70d37-266">Jeśli użytkownik **nie** jest uwierzytelniony, `ChallengeResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="70d37-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="70d37-267">Gdy `ChallengeResult` jest zwracany, użytkownik zostanie przekierowany do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="70d37-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="70d37-268">Gdy użytkownik jest uwierzytelniany, ale nie jest autoryzowany, `ForbidResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="70d37-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="70d37-269">Gdy `ForbidResult` jest zwracany, użytkownik zostanie przekierowany do strony odmowa dostępu.</span><span class="sxs-lookup"><span data-stu-id="70d37-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="70d37-270">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="70d37-270">Test the completed app</span></span>

<span data-ttu-id="70d37-271">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="70d37-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="70d37-272">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="70d37-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="70d37-273">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="70d37-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="70d37-274">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="70d37-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="70d37-275">Jeśli aplikacja ma kontakty:</span><span class="sxs-lookup"><span data-stu-id="70d37-275">If the app has contacts:</span></span>

* <span data-ttu-id="70d37-276">Usuń wszystkie rekordy z `Contact` tabeli.</span><span class="sxs-lookup"><span data-stu-id="70d37-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="70d37-277">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="70d37-278">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="70d37-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="70d37-279">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="70d37-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="70d37-280">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="70d37-281">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="70d37-281">Verify the following operations:</span></span>

* <span data-ttu-id="70d37-282">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="70d37-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="70d37-283">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="70d37-284">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="70d37-285">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="70d37-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="70d37-286">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="70d37-287">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="70d37-287">User</span></span>                | <span data-ttu-id="70d37-288">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="70d37-288">Seeded by the app</span></span> | <span data-ttu-id="70d37-289">Opcje</span><span class="sxs-lookup"><span data-stu-id="70d37-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="70d37-290">Nie</span><span class="sxs-lookup"><span data-stu-id="70d37-290">No</span></span>                | <span data-ttu-id="70d37-291">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="70d37-292">Tak</span><span class="sxs-lookup"><span data-stu-id="70d37-292">Yes</span></span>               | <span data-ttu-id="70d37-293">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="70d37-294">Tak</span><span class="sxs-lookup"><span data-stu-id="70d37-294">Yes</span></span>               | <span data-ttu-id="70d37-295">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="70d37-296">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="70d37-297">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="70d37-298">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="70d37-299">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="70d37-299">Create the starter app</span></span>

* <span data-ttu-id="70d37-300">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="70d37-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="70d37-301">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="70d37-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="70d37-302">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="70d37-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="70d37-303">`-uld`Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="70d37-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="70d37-304">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="70d37-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="70d37-305">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="70d37-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="70d37-306">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="70d37-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="70d37-307">Jeśli wystąpi usterka z `dotnet aspnet-codegenerator razorpage` poleceniem, zobacz [ten problem](https://github.com/aspnet/Scaffolding/issues/984)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="70d37-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="70d37-308">Zaktualizuj kotwicę **ContactManager** w pliku *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="70d37-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="70d37-309">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="70d37-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="70d37-310">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="70d37-310">Seed the database</span></span>

<span data-ttu-id="70d37-311">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="70d37-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="70d37-312">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="70d37-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="70d37-313">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-313">Test that the app seeded the database.</span></span> <span data-ttu-id="70d37-314">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="70d37-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="70d37-315">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="70d37-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="70d37-316">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="70d37-317">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="70d37-317">There are three security groups:</span></span>

* <span data-ttu-id="70d37-318">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="70d37-319">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="70d37-320">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="70d37-321">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="70d37-322">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="70d37-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="70d37-323">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="70d37-324">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="70d37-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="70d37-325">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="70d37-327">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="70d37-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="70d37-329">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="70d37-329">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="70d37-331">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="70d37-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="70d37-332">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="70d37-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="70d37-334">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="70d37-334">The administrator has all privileges.</span></span> <span data-ttu-id="70d37-335">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="70d37-336">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="70d37-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="70d37-337">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="70d37-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="70d37-338">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="70d37-339">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="70d37-340">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="70d37-341">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="70d37-341">Prerequisites</span></span>

<span data-ttu-id="70d37-342">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="70d37-342">This tutorial is advanced.</span></span> <span data-ttu-id="70d37-343">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="70d37-343">You should be familiar with:</span></span>

* [<span data-ttu-id="70d37-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70d37-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="70d37-345">Uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="70d37-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="70d37-346">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="70d37-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="70d37-347">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="70d37-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="70d37-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="70d37-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="70d37-349">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="70d37-349">The starter and completed app</span></span>

<span data-ttu-id="70d37-350">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="70d37-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="70d37-351">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="70d37-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="70d37-352">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="70d37-352">The starter app</span></span>

<span data-ttu-id="70d37-353">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="70d37-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="70d37-354">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="70d37-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="70d37-355">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="70d37-355">Secure user data</span></span>

<span data-ttu-id="70d37-356">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="70d37-357">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="70d37-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="70d37-358">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="70d37-358">Tie the contact data to the user</span></span>

<span data-ttu-id="70d37-359">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="70d37-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="70d37-360">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="70d37-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="70d37-361">`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="70d37-362">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="70d37-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="70d37-363">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="70d37-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="70d37-364">Dodawanie usług ról do programuIdentity</span><span class="sxs-lookup"><span data-stu-id="70d37-364">Add Role services to Identity</span></span>

<span data-ttu-id="70d37-365">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="70d37-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="70d37-366">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="70d37-366">Require authenticated users</span></span>

<span data-ttu-id="70d37-367">Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="70d37-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="70d37-368">Można zrezygnować z uwierzytelniania na Razor poziomie strony, kontrolera lub metody akcji przy użyciu `[AllowAnonymous]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="70d37-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="70d37-369">Ustawienie domyślnych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="70d37-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="70d37-370">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="70d37-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="70d37-371">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do strony indeks, informacje i kontakty, dzięki czemu anonimowi użytkownicy mogą uzyskać informacje o witrynie przed ich zarejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="70d37-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="70d37-372">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="70d37-372">Configure the test account</span></span>

<span data-ttu-id="70d37-373">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="70d37-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="70d37-374">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="70d37-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="70d37-375">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="70d37-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="70d37-376">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="70d37-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="70d37-377">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="70d37-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="70d37-378">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="70d37-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="70d37-379">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="70d37-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="70d37-380">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="70d37-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="70d37-381">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="70d37-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="70d37-382">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="70d37-383">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="70d37-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="70d37-384">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="70d37-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="70d37-385">Utwórz folder *autoryzacji* i Utwórz `ContactIsOwnerAuthorizationHandler` w nim klasę.</span><span class="sxs-lookup"><span data-stu-id="70d37-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="70d37-386">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="70d37-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="70d37-387">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="70d37-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="70d37-388">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="70d37-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="70d37-389">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="70d37-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="70d37-390">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="70d37-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="70d37-391">`Task.CompletedTask`nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="70d37-392">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="70d37-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="70d37-393">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="70d37-394">`ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="70d37-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="70d37-395">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="70d37-395">Create a manager authorization handler</span></span>

<span data-ttu-id="70d37-396">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="70d37-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70d37-397">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="70d37-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="70d37-398">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="70d37-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="70d37-399">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="70d37-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="70d37-400">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="70d37-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="70d37-401">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="70d37-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="70d37-402">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="70d37-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="70d37-403">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="70d37-403">Register the authorization handlers</span></span>

<span data-ttu-id="70d37-404">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="70d37-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="70d37-405">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="70d37-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="70d37-406">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="70d37-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="70d37-407">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70d37-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="70d37-408">`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="70d37-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="70d37-409">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="70d37-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="70d37-410">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="70d37-410">Support authorization</span></span>

<span data-ttu-id="70d37-411">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="70d37-412">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="70d37-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="70d37-413">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="70d37-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="70d37-414">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="70d37-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="70d37-415">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="70d37-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="70d37-416">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="70d37-417">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="70d37-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="70d37-418">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="70d37-418">The preceding code:</span></span>

* <span data-ttu-id="70d37-419">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="70d37-420">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="70d37-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="70d37-421">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="70d37-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="70d37-422">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="70d37-422">Update the CreateModel</span></span>

<span data-ttu-id="70d37-423">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="70d37-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="70d37-424">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="70d37-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="70d37-425">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="70d37-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="70d37-426">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="70d37-427">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="70d37-427">Update the IndexModel</span></span>

<span data-ttu-id="70d37-428">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="70d37-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="70d37-429">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="70d37-429">Update the EditModel</span></span>

<span data-ttu-id="70d37-430">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="70d37-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="70d37-431">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="70d37-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="70d37-432">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="70d37-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="70d37-433">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="70d37-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="70d37-434">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="70d37-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="70d37-435">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="70d37-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="70d37-436">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="70d37-436">Update the DeleteModel</span></span>

<span data-ttu-id="70d37-437">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="70d37-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="70d37-438">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="70d37-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="70d37-439">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="70d37-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="70d37-440">Wsuń usługę autoryzacji w pliku *views/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="70d37-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="70d37-441">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="70d37-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="70d37-442">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="70d37-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="70d37-443">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="70d37-444">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="70d37-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="70d37-445">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="70d37-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="70d37-446">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="70d37-447">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="70d37-447">Update Details</span></span>

<span data-ttu-id="70d37-448">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="70d37-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="70d37-449">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="70d37-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="70d37-450">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="70d37-450">Add or remove a user to a role</span></span>

<span data-ttu-id="70d37-451">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="70d37-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="70d37-452">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-452">Removing privileges from a user.</span></span> <span data-ttu-id="70d37-453">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="70d37-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="70d37-454">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="70d37-455">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="70d37-455">Test the completed app</span></span>

<span data-ttu-id="70d37-456">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="70d37-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="70d37-457">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="70d37-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="70d37-458">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="70d37-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="70d37-459">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="70d37-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="70d37-460">Porzuć i zaktualizuj bazę danych</span><span class="sxs-lookup"><span data-stu-id="70d37-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="70d37-461">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="70d37-462">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="70d37-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="70d37-463">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="70d37-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="70d37-464">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="70d37-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="70d37-465">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="70d37-465">Verify the following operations:</span></span>

* <span data-ttu-id="70d37-466">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="70d37-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="70d37-467">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="70d37-468">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="70d37-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="70d37-469">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="70d37-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="70d37-470">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="70d37-471">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="70d37-471">User</span></span>                | <span data-ttu-id="70d37-472">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="70d37-472">Seeded by the app</span></span> | <span data-ttu-id="70d37-473">Opcje</span><span class="sxs-lookup"><span data-stu-id="70d37-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="70d37-474">Nie</span><span class="sxs-lookup"><span data-stu-id="70d37-474">No</span></span>                | <span data-ttu-id="70d37-475">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="70d37-476">Tak</span><span class="sxs-lookup"><span data-stu-id="70d37-476">Yes</span></span>               | <span data-ttu-id="70d37-477">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="70d37-478">Tak</span><span class="sxs-lookup"><span data-stu-id="70d37-478">Yes</span></span>               | <span data-ttu-id="70d37-479">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="70d37-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="70d37-480">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="70d37-481">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="70d37-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="70d37-482">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="70d37-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="70d37-483">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="70d37-483">Create the starter app</span></span>

* <span data-ttu-id="70d37-484">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="70d37-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="70d37-485">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="70d37-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="70d37-486">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="70d37-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="70d37-487">`-uld`Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="70d37-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="70d37-488">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="70d37-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="70d37-489">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="70d37-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="70d37-490">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="70d37-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="70d37-491">Zaktualizuj kotwicę **ContactManager** w pliku *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="70d37-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="70d37-492">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="70d37-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="70d37-493">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="70d37-493">Seed the database</span></span>

<span data-ttu-id="70d37-494">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) do folderu *danych* .</span><span class="sxs-lookup"><span data-stu-id="70d37-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="70d37-495">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="70d37-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="70d37-496">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="70d37-496">Test that the app seeded the database.</span></span> <span data-ttu-id="70d37-497">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="70d37-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="70d37-498">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="70d37-498">Additional resources</span></span>

* [<span data-ttu-id="70d37-499">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="70d37-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="70d37-500">[ASP.NET Core laboratorium autoryzacji](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="70d37-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="70d37-501">To laboratorium prowadzi do bardziej szczegółowych informacji na temat funkcji zabezpieczeń wprowadzonych w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="70d37-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="70d37-502">Niestandardowa Autoryzacja oparta na zasadach</span><span class="sxs-lookup"><span data-stu-id="70d37-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
