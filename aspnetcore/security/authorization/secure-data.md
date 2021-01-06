---
title: Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację. Obejmuje protokół HTTPS, uwierzytelnianie, zabezpieczenia, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854655"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="a23fa-104">Tworzenie aplikacji sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="a23fa-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="a23fa-105">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="a23fa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a23fa-106">Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="a23fa-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a23fa-107">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="a23fa-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a23fa-108">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a23fa-109">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="a23fa-109">There are three security groups:</span></span>

* <span data-ttu-id="a23fa-110">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a23fa-111">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a23fa-112">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a23fa-113">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a23fa-114">Obrazy w tym dokumencie nie są dokładnie zgodne z najnowszymi szablonami.</span><span class="sxs-lookup"><span data-stu-id="a23fa-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="a23fa-115">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a23fa-116">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a23fa-117">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="a23fa-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a23fa-118">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="a23fa-120">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="a23fa-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="a23fa-122">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="a23fa-122">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="a23fa-124">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a23fa-125">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="a23fa-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="a23fa-127">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="a23fa-127">The administrator has all privileges.</span></span> <span data-ttu-id="a23fa-128">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a23fa-129">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a23fa-130">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a23fa-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a23fa-131">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a23fa-132">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a23fa-133">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a23fa-134">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a23fa-134">Prerequisites</span></span>

<span data-ttu-id="a23fa-135">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-135">This tutorial is advanced.</span></span> <span data-ttu-id="a23fa-136">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="a23fa-136">You should be familiar with:</span></span>

* [<span data-ttu-id="a23fa-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a23fa-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a23fa-138">Authentication</span><span class="sxs-lookup"><span data-stu-id="a23fa-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a23fa-139">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="a23fa-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a23fa-140">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="a23fa-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a23fa-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a23fa-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a23fa-142">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="a23fa-142">The starter and completed app</span></span>

<span data-ttu-id="a23fa-143">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="a23fa-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a23fa-144">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a23fa-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a23fa-145">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="a23fa-145">The starter app</span></span>

<span data-ttu-id="a23fa-146">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="a23fa-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a23fa-147">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="a23fa-148">Aby utworzyć aplikację startową, zobacz [Tworzenie aplikacji Starter](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="a23fa-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a23fa-149">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="a23fa-149">Secure user data</span></span>

<span data-ttu-id="a23fa-150">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a23fa-151">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a23fa-152">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="a23fa-152">Tie the contact data to the user</span></span>

<span data-ttu-id="a23fa-153">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a23fa-154">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a23fa-155">`OwnerID` jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a23fa-156">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a23fa-157">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="a23fa-158">Dodawanie usług ról do programu Identity</span><span class="sxs-lookup"><span data-stu-id="a23fa-158">Add Role services to Identity</span></span>

<span data-ttu-id="a23fa-159">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="a23fa-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="a23fa-160">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="a23fa-160">Require authenticated users</span></span>

<span data-ttu-id="a23fa-161">Ustaw rezerwowe zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="a23fa-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="a23fa-162">Poprzedni wyróżniony kod ustawia [rezerwowe zasady uwierzytelniania](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="a23fa-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="a23fa-163">Rezerwowe zasady uwierzytelniania wymagają \**_Wszyscy_* użytkownicy mają być uwierzytelniani, z wyjątkiem Razor stron, kontrolerów lub metod akcji z atrybutem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="a23fa-164">Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu uwierzytelniania zamiast rezerwowych zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="a23fa-165">Rezerwowe zasady uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="a23fa-165">The fallback authentication policy:</span></span>

<span data-ttu-id="a23fa-166">_ Stosuje się do wszystkich żądań, które nie określają jawnie zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="a23fa-167">W przypadku żądań obsłużonych przez Routing punktów końcowych może to obejmować wszystkie punkty końcowe, które nie określają atrybutu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="a23fa-168">W przypadku żądań obsłużonych przez inne oprogramowanie pośredniczące po wyzwoleniu na oprogramowanie pośredniczące, takie jak [pliki statyczne](xref:fundamentals/static-files), zasady te będą stosowane do wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="a23fa-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="a23fa-169">Ustawienie rezerwowych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="a23fa-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a23fa-170">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a23fa-171"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Klasa zawiera również <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a23fa-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a23fa-172">`DefaultPolicy`To zasady używane z `[Authorize]` atrybutem, jeśli nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="a23fa-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="a23fa-173">`[Authorize]` nie zawiera nazwanych zasad, w przeciwieństwie do `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="a23fa-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="a23fa-174">Aby uzyskać więcej informacji na temat zasad, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="a23fa-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="a23fa-175">Alternatywny sposób, w jaki kontrolery MVC i Razor strony wymagające uwierzytelnienia wszystkich użytkowników są dodawane filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a23fa-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="a23fa-176">Poprzedni kod używa filtru autoryzacji, ustawienie zasad powrotu używa routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="a23fa-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="a23fa-177">Ustawienie zasad powrotu jest preferowanym sposobem wymagania uwierzytelniania wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="a23fa-178">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do `Index` stron i, `Privacy` aby anonimowi użytkownicy mogli uzyskać informacje o witrynie przed zarejestrowaniem:</span><span class="sxs-lookup"><span data-stu-id="a23fa-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a23fa-179">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="a23fa-179">Configure the test account</span></span>

<span data-ttu-id="a23fa-180">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="a23fa-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a23fa-181">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="a23fa-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a23fa-182">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="a23fa-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a23fa-183">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a23fa-184">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a23fa-185">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="a23fa-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a23fa-186">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="a23fa-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a23fa-187">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a23fa-188">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="a23fa-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a23fa-189">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a23fa-190">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="a23fa-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a23fa-191">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="a23fa-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a23fa-192">Utwórz `ContactIsOwnerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a23fa-193">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a23fa-194">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="a23fa-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a23fa-195">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="a23fa-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="a23fa-196">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="a23fa-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a23fa-197">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="a23fa-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a23fa-198">`Task.CompletedTask` nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a23fa-199">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a23fa-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a23fa-200">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a23fa-201">`ContactIsOwnerAuthorizationHandler` nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a23fa-202">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="a23fa-202">Create a manager authorization handler</span></span>

<span data-ttu-id="a23fa-203">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a23fa-204">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a23fa-205">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="a23fa-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a23fa-206">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="a23fa-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="a23fa-207">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a23fa-208">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a23fa-209">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="a23fa-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a23fa-210">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-210">Register the authorization handlers</span></span>

<span data-ttu-id="a23fa-211">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a23fa-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a23fa-212">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a23fa-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a23fa-213">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="a23fa-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a23fa-214">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a23fa-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="a23fa-215">`ContactAdministratorsAuthorizationHandler` i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="a23fa-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a23fa-216">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="a23fa-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a23fa-217">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-217">Support authorization</span></span>

<span data-ttu-id="a23fa-218">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a23fa-219">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="a23fa-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="a23fa-220">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="a23fa-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a23fa-221">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="a23fa-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="a23fa-222">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="a23fa-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a23fa-223">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a23fa-224">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="a23fa-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a23fa-225">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="a23fa-225">The preceding code:</span></span>

* <span data-ttu-id="a23fa-226">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a23fa-227">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="a23fa-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a23fa-228">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="a23fa-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a23fa-229">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="a23fa-229">Update the CreateModel</span></span>

<span data-ttu-id="a23fa-230">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="a23fa-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a23fa-231">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="a23fa-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a23fa-232">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a23fa-233">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a23fa-234">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-234">Update the IndexModel</span></span>

<span data-ttu-id="a23fa-235">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a23fa-236">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-236">Update the EditModel</span></span>

<span data-ttu-id="a23fa-237">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="a23fa-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a23fa-238">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="a23fa-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a23fa-239">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a23fa-240">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="a23fa-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a23fa-241">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="a23fa-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a23fa-242">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a23fa-243">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-243">Update the DeleteModel</span></span>

<span data-ttu-id="a23fa-244">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="a23fa-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a23fa-245">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="a23fa-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="a23fa-246">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="a23fa-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a23fa-247">Wsuń usługę autoryzacji w pliku *Pages/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="a23fa-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a23fa-248">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a23fa-249">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="a23fa-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a23fa-250">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a23fa-251">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="a23fa-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a23fa-252">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="a23fa-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a23fa-253">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a23fa-254">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-254">Update Details</span></span>

<span data-ttu-id="a23fa-255">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="a23fa-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a23fa-256">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="a23fa-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a23fa-257">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="a23fa-257">Add or remove a user to a role</span></span>

<span data-ttu-id="a23fa-258">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="a23fa-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a23fa-259">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-259">Removing privileges from a user.</span></span> <span data-ttu-id="a23fa-260">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a23fa-261">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="a23fa-262">Różnice między wyzwaniem i Zabroń</span><span class="sxs-lookup"><span data-stu-id="a23fa-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="a23fa-263">Ta aplikacja ustawia zasady domyślne, aby [wymagać uwierzytelnionych użytkowników](#rau).</span><span class="sxs-lookup"><span data-stu-id="a23fa-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="a23fa-264">Poniższy kod umożliwia anonimowym użytkownikom.</span><span class="sxs-lookup"><span data-stu-id="a23fa-264">The following code allows anonymous users.</span></span> <span data-ttu-id="a23fa-265">Użytkownicy anonimowi mogą wyświetlać różnice między wyzwaniem a zabranianiem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="a23fa-266">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="a23fa-266">In the preceding code:</span></span>

* <span data-ttu-id="a23fa-267">Jeśli użytkownik **nie** jest uwierzytelniony, `ChallengeResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="a23fa-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="a23fa-268">Gdy `ChallengeResult` jest zwracany, użytkownik zostanie przekierowany do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="a23fa-269">Gdy użytkownik jest uwierzytelniany, ale nie jest autoryzowany, `ForbidResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="a23fa-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="a23fa-270">Gdy `ForbidResult` jest zwracany, użytkownik zostanie przekierowany do strony odmowa dostępu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a23fa-271">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-271">Test the completed app</span></span>

<span data-ttu-id="a23fa-272">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="a23fa-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a23fa-273">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a23fa-274">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="a23fa-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a23fa-275">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="a23fa-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="a23fa-276">Jeśli aplikacja ma kontakty:</span><span class="sxs-lookup"><span data-stu-id="a23fa-276">If the app has contacts:</span></span>

* <span data-ttu-id="a23fa-277">Usuń wszystkie rekordy z `Contact` tabeli.</span><span class="sxs-lookup"><span data-stu-id="a23fa-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="a23fa-278">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="a23fa-279">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="a23fa-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a23fa-280">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="a23fa-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a23fa-281">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a23fa-282">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="a23fa-282">Verify the following operations:</span></span>

* <span data-ttu-id="a23fa-283">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="a23fa-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a23fa-284">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a23fa-285">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a23fa-286">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="a23fa-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a23fa-287">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a23fa-288">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="a23fa-288">User</span></span>                | <span data-ttu-id="a23fa-289">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="a23fa-289">Seeded by the app</span></span> | <span data-ttu-id="a23fa-290">Opcje</span><span class="sxs-lookup"><span data-stu-id="a23fa-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a23fa-291">Nie</span><span class="sxs-lookup"><span data-stu-id="a23fa-291">No</span></span>                | <span data-ttu-id="a23fa-292">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a23fa-293">Yes</span><span class="sxs-lookup"><span data-stu-id="a23fa-293">Yes</span></span>               | <span data-ttu-id="a23fa-294">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a23fa-295">Yes</span><span class="sxs-lookup"><span data-stu-id="a23fa-295">Yes</span></span>               | <span data-ttu-id="a23fa-296">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a23fa-297">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a23fa-298">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a23fa-299">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a23fa-300">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="a23fa-300">Create the starter app</span></span>

* <span data-ttu-id="a23fa-301">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="a23fa-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a23fa-302">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="a23fa-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a23fa-303">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a23fa-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a23fa-304">`-uld` Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="a23fa-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a23fa-305">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="a23fa-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a23fa-306">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a23fa-307">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="a23fa-308">Jeśli wystąpi usterka z `dotnet aspnet-codegenerator razorpage` poleceniem, zobacz [ten problem](https://github.com/aspnet/Scaffolding/issues/984)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="a23fa-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="a23fa-309">Zaktualizuj kotwicę **ContactManager** w pliku *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a23fa-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="a23fa-310">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="a23fa-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a23fa-311">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="a23fa-311">Seed the database</span></span>

<span data-ttu-id="a23fa-312">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="a23fa-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="a23fa-313">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="a23fa-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="a23fa-314">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-314">Test that the app seeded the database.</span></span> <span data-ttu-id="a23fa-315">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="a23fa-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="a23fa-316">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="a23fa-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="a23fa-317">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="a23fa-318">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="a23fa-318">There are three security groups:</span></span>

* <span data-ttu-id="a23fa-319">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="a23fa-320">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="a23fa-321">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="a23fa-322">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="a23fa-323">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="a23fa-324">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="a23fa-325">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="a23fa-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="a23fa-326">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="a23fa-328">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="a23fa-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="a23fa-330">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="a23fa-330">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="a23fa-332">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="a23fa-333">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="a23fa-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="a23fa-335">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="a23fa-335">The administrator has all privileges.</span></span> <span data-ttu-id="a23fa-336">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="a23fa-337">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="a23fa-338">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="a23fa-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="a23fa-339">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="a23fa-340">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="a23fa-341">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a23fa-342">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a23fa-342">Prerequisites</span></span>

<span data-ttu-id="a23fa-343">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-343">This tutorial is advanced.</span></span> <span data-ttu-id="a23fa-344">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="a23fa-344">You should be familiar with:</span></span>

* [<span data-ttu-id="a23fa-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a23fa-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="a23fa-346">Authentication</span><span class="sxs-lookup"><span data-stu-id="a23fa-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a23fa-347">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="a23fa-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="a23fa-348">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="a23fa-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="a23fa-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a23fa-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="a23fa-350">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="a23fa-350">The starter and completed app</span></span>

<span data-ttu-id="a23fa-351">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="a23fa-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="a23fa-352">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="a23fa-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="a23fa-353">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="a23fa-353">The starter app</span></span>

<span data-ttu-id="a23fa-354">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="a23fa-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="a23fa-355">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="a23fa-356">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="a23fa-356">Secure user data</span></span>

<span data-ttu-id="a23fa-357">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="a23fa-358">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="a23fa-359">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="a23fa-359">Tie the contact data to the user</span></span>

<span data-ttu-id="a23fa-360">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="a23fa-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="a23fa-361">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="a23fa-362">`OwnerID` jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="a23fa-363">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="a23fa-364">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="a23fa-365">Dodawanie usług ról do programu Identity</span><span class="sxs-lookup"><span data-stu-id="a23fa-365">Add Role services to Identity</span></span>

<span data-ttu-id="a23fa-366">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="a23fa-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="a23fa-367">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="a23fa-367">Require authenticated users</span></span>

<span data-ttu-id="a23fa-368">Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="a23fa-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="a23fa-369">Można zrezygnować z uwierzytelniania na Razor poziomie strony, kontrolera lub metody akcji przy użyciu `[AllowAnonymous]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="a23fa-370">Ustawienie domyślnych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="a23fa-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="a23fa-371">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="a23fa-372">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do strony indeks, informacje i kontakty, dzięki czemu anonimowi użytkownicy mogą uzyskać informacje o witrynie przed ich zarejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="a23fa-373">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="a23fa-373">Configure the test account</span></span>

<span data-ttu-id="a23fa-374">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="a23fa-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="a23fa-375">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="a23fa-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="a23fa-376">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="a23fa-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="a23fa-377">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a23fa-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="a23fa-378">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="a23fa-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="a23fa-379">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="a23fa-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="a23fa-380">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="a23fa-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="a23fa-381">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="a23fa-382">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="a23fa-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="a23fa-383">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="a23fa-384">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="a23fa-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="a23fa-385">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="a23fa-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="a23fa-386">Utwórz folder *autoryzacji* i Utwórz `ContactIsOwnerAuthorizationHandler` w nim klasę.</span><span class="sxs-lookup"><span data-stu-id="a23fa-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="a23fa-387">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="a23fa-388">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="a23fa-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="a23fa-389">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="a23fa-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="a23fa-390">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="a23fa-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="a23fa-391">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="a23fa-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="a23fa-392">`Task.CompletedTask` nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="a23fa-393">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="a23fa-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="a23fa-394">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="a23fa-395">`ContactIsOwnerAuthorizationHandler` nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="a23fa-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="a23fa-396">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="a23fa-396">Create a manager authorization handler</span></span>

<span data-ttu-id="a23fa-397">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a23fa-398">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="a23fa-399">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="a23fa-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="a23fa-400">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="a23fa-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="a23fa-401">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="a23fa-402">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="a23fa-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="a23fa-403">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="a23fa-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="a23fa-404">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-404">Register the authorization handlers</span></span>

<span data-ttu-id="a23fa-405">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="a23fa-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="a23fa-406">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a23fa-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="a23fa-407">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="a23fa-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a23fa-408">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a23fa-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="a23fa-409">`ContactAdministratorsAuthorizationHandler` i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="a23fa-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="a23fa-410">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="a23fa-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="a23fa-411">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-411">Support authorization</span></span>

<span data-ttu-id="a23fa-412">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="a23fa-413">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="a23fa-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="a23fa-414">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="a23fa-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="a23fa-415">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="a23fa-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="a23fa-416">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="a23fa-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="a23fa-417">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="a23fa-418">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="a23fa-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="a23fa-419">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="a23fa-419">The preceding code:</span></span>

* <span data-ttu-id="a23fa-420">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="a23fa-421">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="a23fa-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="a23fa-422">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="a23fa-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="a23fa-423">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="a23fa-423">Update the CreateModel</span></span>

<span data-ttu-id="a23fa-424">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="a23fa-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="a23fa-425">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="a23fa-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="a23fa-426">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="a23fa-427">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="a23fa-428">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-428">Update the IndexModel</span></span>

<span data-ttu-id="a23fa-429">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="a23fa-430">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-430">Update the EditModel</span></span>

<span data-ttu-id="a23fa-431">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="a23fa-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="a23fa-432">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="a23fa-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="a23fa-433">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="a23fa-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="a23fa-434">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="a23fa-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="a23fa-435">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="a23fa-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="a23fa-436">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="a23fa-437">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="a23fa-437">Update the DeleteModel</span></span>

<span data-ttu-id="a23fa-438">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="a23fa-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="a23fa-439">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="a23fa-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="a23fa-440">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="a23fa-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="a23fa-441">Wsuń usługę autoryzacji w pliku *views/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="a23fa-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="a23fa-442">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="a23fa-443">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="a23fa-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="a23fa-444">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="a23fa-445">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="a23fa-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="a23fa-446">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="a23fa-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="a23fa-447">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="a23fa-448">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-448">Update Details</span></span>

<span data-ttu-id="a23fa-449">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="a23fa-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="a23fa-450">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="a23fa-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="a23fa-451">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="a23fa-451">Add or remove a user to a role</span></span>

<span data-ttu-id="a23fa-452">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="a23fa-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="a23fa-453">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-453">Removing privileges from a user.</span></span> <span data-ttu-id="a23fa-454">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="a23fa-455">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="a23fa-456">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="a23fa-456">Test the completed app</span></span>

<span data-ttu-id="a23fa-457">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="a23fa-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="a23fa-458">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="a23fa-459">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="a23fa-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="a23fa-460">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="a23fa-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="a23fa-461">Porzuć i zaktualizuj bazę danych</span><span class="sxs-lookup"><span data-stu-id="a23fa-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="a23fa-462">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="a23fa-463">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="a23fa-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="a23fa-464">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="a23fa-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="a23fa-465">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a23fa-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="a23fa-466">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="a23fa-466">Verify the following operations:</span></span>

* <span data-ttu-id="a23fa-467">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="a23fa-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="a23fa-468">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="a23fa-469">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="a23fa-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="a23fa-470">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="a23fa-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="a23fa-471">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="a23fa-472">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="a23fa-472">User</span></span>                | <span data-ttu-id="a23fa-473">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="a23fa-473">Seeded by the app</span></span> | <span data-ttu-id="a23fa-474">Opcje</span><span class="sxs-lookup"><span data-stu-id="a23fa-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="a23fa-475">Nie</span><span class="sxs-lookup"><span data-stu-id="a23fa-475">No</span></span>                | <span data-ttu-id="a23fa-476">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="a23fa-477">Yes</span><span class="sxs-lookup"><span data-stu-id="a23fa-477">Yes</span></span>               | <span data-ttu-id="a23fa-478">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="a23fa-479">Yes</span><span class="sxs-lookup"><span data-stu-id="a23fa-479">Yes</span></span>               | <span data-ttu-id="a23fa-480">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="a23fa-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="a23fa-481">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="a23fa-482">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="a23fa-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="a23fa-483">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="a23fa-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="a23fa-484">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="a23fa-484">Create the starter app</span></span>

* <span data-ttu-id="a23fa-485">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="a23fa-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="a23fa-486">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="a23fa-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="a23fa-487">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="a23fa-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="a23fa-488">`-uld` Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="a23fa-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="a23fa-489">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="a23fa-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="a23fa-490">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="a23fa-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="a23fa-491">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="a23fa-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="a23fa-492">Zaktualizuj kotwicę **ContactManager** w pliku *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a23fa-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="a23fa-493">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="a23fa-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="a23fa-494">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="a23fa-494">Seed the database</span></span>

<span data-ttu-id="a23fa-495">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) do folderu *danych* .</span><span class="sxs-lookup"><span data-stu-id="a23fa-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="a23fa-496">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="a23fa-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="a23fa-497">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="a23fa-497">Test that the app seeded the database.</span></span> <span data-ttu-id="a23fa-498">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="a23fa-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="a23fa-499">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a23fa-499">Additional resources</span></span>

* [<span data-ttu-id="a23fa-500">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a23fa-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="a23fa-501">[ASP.NET Core laboratorium autoryzacji](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="a23fa-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="a23fa-502">To laboratorium prowadzi do bardziej szczegółowych informacji na temat funkcji zabezpieczeń wprowadzonych w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="a23fa-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="a23fa-503">Niestandardowa Autoryzacja oparta na zasadach</span><span class="sxs-lookup"><span data-stu-id="a23fa-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
