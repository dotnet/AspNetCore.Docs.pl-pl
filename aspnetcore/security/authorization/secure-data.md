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
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689308"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="8c989-104">Tworzenie aplikacji sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="8c989-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="8c989-105">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="8c989-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8c989-106">Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="8c989-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c989-107">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="8c989-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="8c989-108">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="8c989-109">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="8c989-109">There are three security groups:</span></span>

* <span data-ttu-id="8c989-110">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="8c989-111">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="8c989-112">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="8c989-113">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="8c989-114">Obrazy w tym dokumencie nie są dokładnie zgodne z najnowszymi szablonami.</span><span class="sxs-lookup"><span data-stu-id="8c989-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="8c989-115">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="8c989-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="8c989-116">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="8c989-117">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="8c989-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="8c989-118">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="8c989-120">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="8c989-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="8c989-122">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="8c989-122">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="8c989-124">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="8c989-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="8c989-125">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="8c989-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="8c989-127">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="8c989-127">The administrator has all privileges.</span></span> <span data-ttu-id="8c989-128">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="8c989-129">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="8c989-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="8c989-130">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8c989-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="8c989-131">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="8c989-132">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="8c989-133">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c989-134">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8c989-134">Prerequisites</span></span>

<span data-ttu-id="8c989-135">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="8c989-135">This tutorial is advanced.</span></span> <span data-ttu-id="8c989-136">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="8c989-136">You should be familiar with:</span></span>

* [<span data-ttu-id="8c989-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c989-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="8c989-138">Authentication</span><span class="sxs-lookup"><span data-stu-id="8c989-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="8c989-139">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="8c989-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8c989-140">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="8c989-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="8c989-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8c989-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="8c989-142">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="8c989-142">The starter and completed app</span></span>

<span data-ttu-id="8c989-143">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="8c989-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="8c989-144">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="8c989-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="8c989-145">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="8c989-145">The starter app</span></span>

<span data-ttu-id="8c989-146">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="8c989-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="8c989-147">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="8c989-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="8c989-148">Aby utworzyć aplikację startową, zobacz [Tworzenie aplikacji Starter](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="8c989-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="8c989-149">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="8c989-149">Secure user data</span></span>

<span data-ttu-id="8c989-150">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="8c989-151">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="8c989-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="8c989-152">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="8c989-152">Tie the contact data to the user</span></span>

<span data-ttu-id="8c989-153">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="8c989-154">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="8c989-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="8c989-155">`OwnerID` jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="8c989-156">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="8c989-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="8c989-157">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="8c989-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="8c989-158">Dodawanie usług ról do programu Identity</span><span class="sxs-lookup"><span data-stu-id="8c989-158">Add Role services to Identity</span></span>

<span data-ttu-id="8c989-159">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="8c989-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="8c989-160">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="8c989-160">Require authenticated users</span></span>

<span data-ttu-id="8c989-161">Ustaw rezerwowe zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="8c989-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="8c989-162">Poprzedni wyróżniony kod ustawia [rezerwowe zasady uwierzytelniania](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="8c989-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="8c989-163">Rezerwowe zasady uwierzytelniania wymagają \**_Wszyscy_* użytkownicy mają być uwierzytelniani, z wyjątkiem Razor stron, kontrolerów lub metod akcji z atrybutem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8c989-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="8c989-164">Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu uwierzytelniania zamiast rezerwowych zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8c989-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="8c989-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> dodaje <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> do bieżącego wystąpienia, które wymusza, że bieżący użytkownik jest uwierzytelniony.</span><span class="sxs-lookup"><span data-stu-id="8c989-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="8c989-166">Rezerwowe zasady uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="8c989-166">The fallback authentication policy:</span></span>

<span data-ttu-id="8c989-167">_ Stosuje się do wszystkich żądań, które nie określają jawnie zasad uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="8c989-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="8c989-168">W przypadku żądań obsłużonych przez Routing punktów końcowych może to obejmować wszystkie punkty końcowe, które nie określają atrybutu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="8c989-169">W przypadku żądań obsłużonych przez inne oprogramowanie pośredniczące po wyzwoleniu na oprogramowanie pośredniczące, takie jak [pliki statyczne](xref:fundamentals/static-files), zasady te będą stosowane do wszystkich żądań.</span><span class="sxs-lookup"><span data-stu-id="8c989-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="8c989-170">Ustawienie rezerwowych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="8c989-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="8c989-171">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8c989-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="8c989-172"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Klasa zawiera również <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8c989-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8c989-173">`DefaultPolicy`To zasady używane z `[Authorize]` atrybutem, jeśli nie określono żadnych zasad.</span><span class="sxs-lookup"><span data-stu-id="8c989-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="8c989-174">`[Authorize]` nie zawiera nazwanych zasad, w przeciwieństwie do `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="8c989-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="8c989-175">Aby uzyskać więcej informacji na temat zasad, zobacz <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="8c989-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="8c989-176">Alternatywny sposób, w jaki kontrolery MVC i Razor strony wymagające uwierzytelnienia wszystkich użytkowników są dodawane filtr autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8c989-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="8c989-177">Poprzedni kod używa filtru autoryzacji, ustawienie zasad powrotu używa routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8c989-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="8c989-178">Ustawienie zasad powrotu jest preferowanym sposobem wymagania uwierzytelniania wszystkich użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="8c989-179">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do `Index` stron i, `Privacy` aby anonimowi użytkownicy mogli uzyskać informacje o witrynie przed zarejestrowaniem:</span><span class="sxs-lookup"><span data-stu-id="8c989-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="8c989-180">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="8c989-180">Configure the test account</span></span>

<span data-ttu-id="8c989-181">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="8c989-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="8c989-182">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="8c989-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="8c989-183">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="8c989-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="8c989-184">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="8c989-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="8c989-185">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="8c989-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="8c989-186">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="8c989-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="8c989-187">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="8c989-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="8c989-188">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="8c989-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="8c989-189">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="8c989-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="8c989-190">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="8c989-191">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="8c989-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="8c989-192">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="8c989-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="8c989-193">Utwórz `ContactIsOwnerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="8c989-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="8c989-194">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="8c989-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="8c989-195">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="8c989-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="8c989-196">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="8c989-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="8c989-197">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="8c989-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="8c989-198">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="8c989-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="8c989-199">`Task.CompletedTask` nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="8c989-200">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="8c989-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="8c989-201">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="8c989-202">`ContactIsOwnerAuthorizationHandler` nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="8c989-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="8c989-203">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="8c989-203">Create a manager authorization handler</span></span>

<span data-ttu-id="8c989-204">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="8c989-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="8c989-205">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="8c989-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="8c989-206">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="8c989-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="8c989-207">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="8c989-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="8c989-208">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="8c989-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="8c989-209">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="8c989-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="8c989-210">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="8c989-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="8c989-211">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8c989-211">Register the authorization handlers</span></span>

<span data-ttu-id="8c989-212">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="8c989-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="8c989-213">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8c989-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="8c989-214">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="8c989-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8c989-215">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8c989-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="8c989-216">`ContactAdministratorsAuthorizationHandler` i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8c989-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="8c989-217">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="8c989-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="8c989-218">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8c989-218">Support authorization</span></span>

<span data-ttu-id="8c989-219">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="8c989-220">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="8c989-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="8c989-221">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="8c989-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="8c989-222">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="8c989-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="8c989-223">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="8c989-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="8c989-224">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="8c989-225">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="8c989-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="8c989-226">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8c989-226">The preceding code:</span></span>

* <span data-ttu-id="8c989-227">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="8c989-228">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="8c989-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="8c989-229">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="8c989-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="8c989-230">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="8c989-230">Update the CreateModel</span></span>

<span data-ttu-id="8c989-231">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="8c989-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="8c989-232">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="8c989-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="8c989-233">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="8c989-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="8c989-234">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="8c989-235">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="8c989-235">Update the IndexModel</span></span>

<span data-ttu-id="8c989-236">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="8c989-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="8c989-237">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="8c989-237">Update the EditModel</span></span>

<span data-ttu-id="8c989-238">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="8c989-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="8c989-239">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="8c989-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="8c989-240">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="8c989-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="8c989-241">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="8c989-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="8c989-242">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="8c989-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="8c989-243">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="8c989-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="8c989-244">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="8c989-244">Update the DeleteModel</span></span>

<span data-ttu-id="8c989-245">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="8c989-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="8c989-246">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="8c989-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="8c989-247">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="8c989-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="8c989-248">Wsuń usługę autoryzacji w pliku *Pages/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="8c989-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="8c989-249">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="8c989-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="8c989-250">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="8c989-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="8c989-251">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="8c989-252">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="8c989-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="8c989-253">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="8c989-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="8c989-254">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="8c989-255">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="8c989-255">Update Details</span></span>

<span data-ttu-id="8c989-256">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="8c989-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="8c989-257">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="8c989-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="8c989-258">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="8c989-258">Add or remove a user to a role</span></span>

<span data-ttu-id="8c989-259">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="8c989-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="8c989-260">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-260">Removing privileges from a user.</span></span> <span data-ttu-id="8c989-261">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="8c989-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="8c989-262">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="8c989-263">Różnice między wyzwaniem i Zabroń</span><span class="sxs-lookup"><span data-stu-id="8c989-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="8c989-264">Ta aplikacja ustawia zasady domyślne, aby [wymagać uwierzytelnionych użytkowników](#rau).</span><span class="sxs-lookup"><span data-stu-id="8c989-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="8c989-265">Poniższy kod umożliwia anonimowym użytkownikom.</span><span class="sxs-lookup"><span data-stu-id="8c989-265">The following code allows anonymous users.</span></span> <span data-ttu-id="8c989-266">Użytkownicy anonimowi mogą wyświetlać różnice między wyzwaniem a zabranianiem.</span><span class="sxs-lookup"><span data-stu-id="8c989-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="8c989-267">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8c989-267">In the preceding code:</span></span>

* <span data-ttu-id="8c989-268">Jeśli użytkownik **nie** jest uwierzytelniony, `ChallengeResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="8c989-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="8c989-269">Gdy `ChallengeResult` jest zwracany, użytkownik zostanie przekierowany do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="8c989-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="8c989-270">Gdy użytkownik jest uwierzytelniany, ale nie jest autoryzowany, `ForbidResult` zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="8c989-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="8c989-271">Gdy `ForbidResult` jest zwracany, użytkownik zostanie przekierowany do strony odmowa dostępu.</span><span class="sxs-lookup"><span data-stu-id="8c989-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="8c989-272">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="8c989-272">Test the completed app</span></span>

<span data-ttu-id="8c989-273">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="8c989-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="8c989-274">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="8c989-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="8c989-275">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="8c989-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="8c989-276">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="8c989-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="8c989-277">Jeśli aplikacja ma kontakty:</span><span class="sxs-lookup"><span data-stu-id="8c989-277">If the app has contacts:</span></span>

* <span data-ttu-id="8c989-278">Usuń wszystkie rekordy z `Contact` tabeli.</span><span class="sxs-lookup"><span data-stu-id="8c989-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="8c989-279">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="8c989-280">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="8c989-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="8c989-281">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="8c989-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="8c989-282">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="8c989-283">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="8c989-283">Verify the following operations:</span></span>

* <span data-ttu-id="8c989-284">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="8c989-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="8c989-285">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="8c989-286">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="8c989-287">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="8c989-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="8c989-288">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="8c989-289">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="8c989-289">User</span></span>                | <span data-ttu-id="8c989-290">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="8c989-290">Seeded by the app</span></span> | <span data-ttu-id="8c989-291">Opcje</span><span class="sxs-lookup"><span data-stu-id="8c989-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="8c989-292">Nie</span><span class="sxs-lookup"><span data-stu-id="8c989-292">No</span></span>                | <span data-ttu-id="8c989-293">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="8c989-294">Tak</span><span class="sxs-lookup"><span data-stu-id="8c989-294">Yes</span></span>               | <span data-ttu-id="8c989-295">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="8c989-296">Tak</span><span class="sxs-lookup"><span data-stu-id="8c989-296">Yes</span></span>               | <span data-ttu-id="8c989-297">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="8c989-298">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="8c989-299">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="8c989-300">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="8c989-301">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="8c989-301">Create the starter app</span></span>

* <span data-ttu-id="8c989-302">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="8c989-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="8c989-303">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="8c989-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="8c989-304">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8c989-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="8c989-305">`-uld` Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="8c989-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="8c989-306">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="8c989-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="8c989-307">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="8c989-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="8c989-308">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="8c989-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="8c989-309">Jeśli wystąpi usterka z `dotnet aspnet-codegenerator razorpage` poleceniem, zobacz [ten problem](https://github.com/aspnet/Scaffolding/issues/984)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="8c989-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="8c989-310">Zaktualizuj kotwicę **ContactManager** w pliku *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8c989-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="8c989-311">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="8c989-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="8c989-312">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="8c989-312">Seed the database</span></span>

<span data-ttu-id="8c989-313">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="8c989-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="8c989-314">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="8c989-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="8c989-315">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-315">Test that the app seeded the database.</span></span> <span data-ttu-id="8c989-316">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="8c989-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="8c989-317">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="8c989-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="8c989-318">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="8c989-319">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="8c989-319">There are three security groups:</span></span>

* <span data-ttu-id="8c989-320">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="8c989-321">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="8c989-322">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="8c989-323">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="8c989-324">Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="8c989-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="8c989-325">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="8c989-326">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="8c989-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="8c989-327">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="8c989-329">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="8c989-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="8c989-331">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="8c989-331">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="8c989-333">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="8c989-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="8c989-334">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="8c989-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="8c989-336">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="8c989-336">The administrator has all privileges.</span></span> <span data-ttu-id="8c989-337">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="8c989-338">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="8c989-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="8c989-339">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="8c989-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="8c989-340">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="8c989-341">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="8c989-342">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8c989-343">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="8c989-343">Prerequisites</span></span>

<span data-ttu-id="8c989-344">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="8c989-344">This tutorial is advanced.</span></span> <span data-ttu-id="8c989-345">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="8c989-345">You should be familiar with:</span></span>

* [<span data-ttu-id="8c989-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c989-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="8c989-347">Authentication</span><span class="sxs-lookup"><span data-stu-id="8c989-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="8c989-348">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="8c989-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8c989-349">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="8c989-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="8c989-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8c989-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="8c989-351">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="8c989-351">The starter and completed app</span></span>

<span data-ttu-id="8c989-352">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="8c989-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="8c989-353">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="8c989-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="8c989-354">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="8c989-354">The starter app</span></span>

<span data-ttu-id="8c989-355">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="8c989-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="8c989-356">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="8c989-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="8c989-357">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="8c989-357">Secure user data</span></span>

<span data-ttu-id="8c989-358">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="8c989-359">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="8c989-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="8c989-360">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="8c989-360">Tie the contact data to the user</span></span>

<span data-ttu-id="8c989-361">Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8c989-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="8c989-362">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="8c989-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="8c989-363">`OwnerID` jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="8c989-364">`Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="8c989-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="8c989-365">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="8c989-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="8c989-366">Dodawanie usług ról do programu Identity</span><span class="sxs-lookup"><span data-stu-id="8c989-366">Add Role services to Identity</span></span>

<span data-ttu-id="8c989-367">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="8c989-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="8c989-368">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="8c989-368">Require authenticated users</span></span>

<span data-ttu-id="8c989-369">Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="8c989-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="8c989-370">Można zrezygnować z uwierzytelniania na Razor poziomie strony, kontrolera lub metody akcji przy użyciu `[AllowAnonymous]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8c989-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="8c989-371">Ustawienie domyślnych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="8c989-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="8c989-372">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="8c989-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="8c989-373">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do strony indeks, informacje i kontakty, dzięki czemu anonimowi użytkownicy mogą uzyskać informacje o witrynie przed ich zarejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="8c989-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="8c989-374">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="8c989-374">Configure the test account</span></span>

<span data-ttu-id="8c989-375">`SeedData`Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="8c989-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="8c989-376">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="8c989-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="8c989-377">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="8c989-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="8c989-378">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="8c989-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="8c989-379">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="8c989-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="8c989-380">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="8c989-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="8c989-381">Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="8c989-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="8c989-382">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="8c989-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="8c989-383">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="8c989-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="8c989-384">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="8c989-385">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="8c989-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="8c989-386">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="8c989-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="8c989-387">Utwórz folder *autoryzacji* i Utwórz `ContactIsOwnerAuthorizationHandler` w nim klasę.</span><span class="sxs-lookup"><span data-stu-id="8c989-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="8c989-388">`ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="8c989-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="8c989-389">`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="8c989-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="8c989-390">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="8c989-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="8c989-391">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="8c989-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="8c989-392">Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="8c989-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="8c989-393">`Task.CompletedTask` nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="8c989-394">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="8c989-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="8c989-395">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="8c989-396">`ContactIsOwnerAuthorizationHandler` nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="8c989-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="8c989-397">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="8c989-397">Create a manager authorization handler</span></span>

<span data-ttu-id="8c989-398">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="8c989-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="8c989-399">`ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="8c989-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="8c989-400">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="8c989-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="8c989-401">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="8c989-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="8c989-402">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="8c989-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="8c989-403">`ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="8c989-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="8c989-404">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="8c989-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="8c989-405">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8c989-405">Register the authorization handlers</span></span>

<span data-ttu-id="8c989-406">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="8c989-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="8c989-407">`ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8c989-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="8c989-408">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="8c989-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8c989-409">Dodaj następujący kod na końcu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8c989-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="8c989-410">`ContactAdministratorsAuthorizationHandler` i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="8c989-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="8c989-411">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="8c989-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="8c989-412">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="8c989-412">Support authorization</span></span>

<span data-ttu-id="8c989-413">Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="8c989-414">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="8c989-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="8c989-415">Zapoznaj się z `ContactOperations` klasą.</span><span class="sxs-lookup"><span data-stu-id="8c989-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="8c989-416">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="8c989-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="8c989-417">Utwórz klasę bazową dla stron kontaktów Razor</span><span class="sxs-lookup"><span data-stu-id="8c989-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="8c989-418">Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="8c989-419">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="8c989-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="8c989-420">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="8c989-420">The preceding code:</span></span>

* <span data-ttu-id="8c989-421">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="8c989-422">Dodaje Identity `UserManager` usługę.</span><span class="sxs-lookup"><span data-stu-id="8c989-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="8c989-423">Dodaj `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="8c989-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="8c989-424">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="8c989-424">Update the CreateModel</span></span>

<span data-ttu-id="8c989-425">Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:</span><span class="sxs-lookup"><span data-stu-id="8c989-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="8c989-426">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="8c989-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="8c989-427">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="8c989-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="8c989-428">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="8c989-429">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="8c989-429">Update the IndexModel</span></span>

<span data-ttu-id="8c989-430">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="8c989-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="8c989-431">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="8c989-431">Update the EditModel</span></span>

<span data-ttu-id="8c989-432">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="8c989-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="8c989-433">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="8c989-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="8c989-434">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="8c989-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="8c989-435">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="8c989-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="8c989-436">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="8c989-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="8c989-437">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="8c989-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="8c989-438">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="8c989-438">Update the DeleteModel</span></span>

<span data-ttu-id="8c989-439">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="8c989-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="8c989-440">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="8c989-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="8c989-441">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="8c989-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="8c989-442">Wsuń usługę autoryzacji w pliku *views/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="8c989-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="8c989-443">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="8c989-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="8c989-444">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="8c989-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="8c989-445">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="8c989-446">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="8c989-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="8c989-447">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="8c989-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="8c989-448">RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="8c989-449">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="8c989-449">Update Details</span></span>

<span data-ttu-id="8c989-450">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="8c989-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="8c989-451">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="8c989-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="8c989-452">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="8c989-452">Add or remove a user to a role</span></span>

<span data-ttu-id="8c989-453">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="8c989-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="8c989-454">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-454">Removing privileges from a user.</span></span> <span data-ttu-id="8c989-455">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="8c989-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="8c989-456">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="8c989-457">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="8c989-457">Test the completed app</span></span>

<span data-ttu-id="8c989-458">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="8c989-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="8c989-459">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="8c989-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="8c989-460">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="8c989-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="8c989-461">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="8c989-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="8c989-462">Porzuć i zaktualizuj bazę danych</span><span class="sxs-lookup"><span data-stu-id="8c989-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="8c989-463">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="8c989-464">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="8c989-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="8c989-465">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="8c989-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="8c989-466">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8c989-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="8c989-467">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="8c989-467">Verify the following operations:</span></span>

* <span data-ttu-id="8c989-468">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="8c989-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="8c989-469">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="8c989-470">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="8c989-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="8c989-471">`Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="8c989-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="8c989-472">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="8c989-473">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="8c989-473">User</span></span>                | <span data-ttu-id="8c989-474">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="8c989-474">Seeded by the app</span></span> | <span data-ttu-id="8c989-475">Opcje</span><span class="sxs-lookup"><span data-stu-id="8c989-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="8c989-476">Nie</span><span class="sxs-lookup"><span data-stu-id="8c989-476">No</span></span>                | <span data-ttu-id="8c989-477">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="8c989-478">Tak</span><span class="sxs-lookup"><span data-stu-id="8c989-478">Yes</span></span>               | <span data-ttu-id="8c989-479">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="8c989-480">Tak</span><span class="sxs-lookup"><span data-stu-id="8c989-480">Yes</span></span>               | <span data-ttu-id="8c989-481">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="8c989-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="8c989-482">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="8c989-483">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="8c989-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="8c989-484">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="8c989-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="8c989-485">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="8c989-485">Create the starter app</span></span>

* <span data-ttu-id="8c989-486">Tworzenie Razor aplikacji stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="8c989-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="8c989-487">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="8c989-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="8c989-488">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8c989-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="8c989-489">`-uld` Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="8c989-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="8c989-490">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="8c989-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="8c989-491">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="8c989-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="8c989-492">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="8c989-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="8c989-493">Zaktualizuj kotwicę **ContactManager** w pliku *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8c989-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="8c989-494">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="8c989-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="8c989-495">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="8c989-495">Seed the database</span></span>

<span data-ttu-id="8c989-496">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) do folderu *danych* .</span><span class="sxs-lookup"><span data-stu-id="8c989-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="8c989-497">Wywołanie `SeedData.Initialize` z `Main` :</span><span class="sxs-lookup"><span data-stu-id="8c989-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="8c989-498">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="8c989-498">Test that the app seeded the database.</span></span> <span data-ttu-id="8c989-499">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="8c989-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="8c989-500">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="8c989-500">Additional resources</span></span>

* [<span data-ttu-id="8c989-501">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8c989-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="8c989-502">[ASP.NET Core laboratorium autoryzacji](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="8c989-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="8c989-503">To laboratorium prowadzi do bardziej szczegółowych informacji na temat funkcji zabezpieczeń wprowadzonych w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="8c989-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="8c989-504">Niestandardowa Autoryzacja oparta na zasadach</span><span class="sxs-lookup"><span data-stu-id="8c989-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
