---
title: Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację
author: rick-anderson
description: Dowiedz się, jak Razor utworzyć aplikację stron z danymi użytkownika chronionymi przez autoryzację. Obejmuje HTTPS, uwierzytelnianie, zabezpieczenia, ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775755"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="9ff29-104">Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację</span><span class="sxs-lookup"><span data-stu-id="9ff29-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="9ff29-105">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="9ff29-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="9ff29-106">[Ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) jest wyświetlany w wersji ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="9ff29-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="9ff29-107">Wersja ASP.NET Core 1,1 tego samouczka znajduje się w [tym](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folderze.</span><span class="sxs-lookup"><span data-stu-id="9ff29-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="9ff29-108">Przykład 1,1 ASP.NET Core znajduje się w [próbkach](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="9ff29-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9ff29-109">Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="9ff29-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ff29-110">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="9ff29-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="9ff29-111">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="9ff29-112">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="9ff29-112">There are three security groups:</span></span>

* <span data-ttu-id="9ff29-113">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="9ff29-114">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="9ff29-115">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="9ff29-116">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="9ff29-117">Obrazy w tym dokumencie nie są dokładnie zgodne z najnowszymi szablonami.</span><span class="sxs-lookup"><span data-stu-id="9ff29-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="9ff29-118">Na poniższej ilustracji użytkownik Rick (`rick@example.com`) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="9ff29-119">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować**/**Usuń**/**Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="9ff29-120">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="9ff29-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="9ff29-121">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="9ff29-123">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="9ff29-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu manager@contoso.com przedstawiający zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="9ff29-125">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="9ff29-125">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="9ff29-127">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="9ff29-128">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="9ff29-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu admin@contoso.com przedstawiający zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="9ff29-130">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="9ff29-130">The administrator has all privileges.</span></span> <span data-ttu-id="9ff29-131">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="9ff29-132">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="9ff29-133">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="9ff29-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="9ff29-134">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="9ff29-135">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="9ff29-136">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ff29-137">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9ff29-137">Prerequisites</span></span>

<span data-ttu-id="9ff29-138">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-138">This tutorial is advanced.</span></span> <span data-ttu-id="9ff29-139">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="9ff29-139">You should be familiar with:</span></span>

* [<span data-ttu-id="9ff29-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ff29-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="9ff29-141">Authentication</span><span class="sxs-lookup"><span data-stu-id="9ff29-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="9ff29-142">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="9ff29-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="9ff29-143">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="9ff29-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="9ff29-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9ff29-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="9ff29-145">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="9ff29-145">The starter and completed app</span></span>

<span data-ttu-id="9ff29-146">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="9ff29-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="9ff29-147">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="9ff29-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="9ff29-148">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="9ff29-148">The starter app</span></span>

<span data-ttu-id="9ff29-149">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="9ff29-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="9ff29-150">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="9ff29-151">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="9ff29-151">Secure user data</span></span>

<span data-ttu-id="9ff29-152">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="9ff29-153">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="9ff29-154">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="9ff29-154">Tie the contact data to the user</span></span>

<span data-ttu-id="9ff29-155">Użyj identyfikatora użytkownika [tożsamości](xref:security/authentication/identity) ASP.NET, aby upewnić się, że użytkownicy będą mogli edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="9ff29-156">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="9ff29-157">`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w bazie danych [tożsamości](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="9ff29-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="9ff29-158">Pole `Status` określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="9ff29-159">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="9ff29-160">Dodawanie usług ról do tożsamości</span><span class="sxs-lookup"><span data-stu-id="9ff29-160">Add Role services to Identity</span></span>

<span data-ttu-id="9ff29-161">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="9ff29-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="9ff29-162">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="9ff29-162">Require authenticated users</span></span>

<span data-ttu-id="9ff29-163">Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="9ff29-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="9ff29-164">Można zrezygnować z uwierzytelniania na stronie Razor, kontrolerze lub poziomie metody akcji przy użyciu `[AllowAnonymous]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="9ff29-165">Ustawienie domyślnych zasad uwierzytelniania wymagające uwierzytelnienia użytkowników chroniących nowo dodane Razor Pages i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="9ff29-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="9ff29-166">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i Razor Pages uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="9ff29-167">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do stron indeksu i prywatności, aby użytkownicy anonimowi mogli uzyskać informacje o witrynie przed ich zarejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="9ff29-168">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="9ff29-168">Configure the test account</span></span>

<span data-ttu-id="9ff29-169">`SeedData` Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="9ff29-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="9ff29-170">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="9ff29-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="9ff29-171">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9ff29-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="9ff29-172">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="9ff29-173">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="9ff29-174">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="9ff29-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="9ff29-175">Zaktualizuj `Initialize` metodę w `SeedData` klasie, aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="9ff29-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="9ff29-176">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="9ff29-177">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="9ff29-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="9ff29-178">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="9ff29-179">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="9ff29-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="9ff29-180">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="9ff29-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="9ff29-181">Utwórz `ContactIsOwnerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="9ff29-182">`ContactIsOwnerAuthorizationHandler` Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="9ff29-183">Kontekst `ContactIsOwnerAuthorizationHandler` wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="9ff29-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="9ff29-184">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="9ff29-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="9ff29-185">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="9ff29-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="9ff29-186">Zwracaj `Task.CompletedTask` , gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="9ff29-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="9ff29-187">`Task.CompletedTask`nie powiodło się lub&mdash;niepowodzenie — zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="9ff29-188">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="9ff29-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="9ff29-189">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="9ff29-190">`ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="9ff29-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="9ff29-191">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="9ff29-191">Create a manager authorization handler</span></span>

<span data-ttu-id="9ff29-192">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="9ff29-193">`ContactManagerAuthorizationHandler` Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="9ff29-194">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="9ff29-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="9ff29-195">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="9ff29-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="9ff29-196">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="9ff29-197">`ContactAdministratorsAuthorizationHandler` Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="9ff29-198">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="9ff29-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="9ff29-199">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-199">Register the authorization handlers</span></span>

<span data-ttu-id="9ff29-200">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="9ff29-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="9ff29-201">`ContactIsOwnerAuthorizationHandler` Używa ASP.NET Core [tożsamość](xref:security/authentication/identity), która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9ff29-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="9ff29-202">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="9ff29-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9ff29-203">Dodaj następujący kod na końcu `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ff29-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="9ff29-204">`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="9ff29-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="9ff29-205">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują `Context` się w parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="9ff29-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="9ff29-206">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-206">Support authorization</span></span>

<span data-ttu-id="9ff29-207">W tej sekcji należy zaktualizować Razor Pages i dodać klasę wymagania operacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="9ff29-208">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="9ff29-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="9ff29-209">Zapoznaj `ContactOperations` się z klasą.</span><span class="sxs-lookup"><span data-stu-id="9ff29-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="9ff29-210">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="9ff29-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="9ff29-211">Utwórz klasę bazową dla kontaktów Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9ff29-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="9ff29-212">Utwórz klasę bazową zawierającą usługi używane w Razor Pages kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="9ff29-213">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="9ff29-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="9ff29-214">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9ff29-214">The preceding code:</span></span>

* <span data-ttu-id="9ff29-215">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="9ff29-216">Dodaje usługę tożsamości `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="9ff29-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="9ff29-217">Dodaj `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="9ff29-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="9ff29-218">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="9ff29-218">Update the CreateModel</span></span>

<span data-ttu-id="9ff29-219">Zaktualizuj konstruktora Create Page model, aby używał klasy `DI_BasePageModel` bazowej:</span><span class="sxs-lookup"><span data-stu-id="9ff29-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="9ff29-220">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="9ff29-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="9ff29-221">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="9ff29-222">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="9ff29-223">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-223">Update the IndexModel</span></span>

<span data-ttu-id="9ff29-224">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="9ff29-225">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-225">Update the EditModel</span></span>

<span data-ttu-id="9ff29-226">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="9ff29-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="9ff29-227">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="9ff29-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="9ff29-228">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="9ff29-229">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="9ff29-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="9ff29-230">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="9ff29-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="9ff29-231">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="9ff29-232">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-232">Update the DeleteModel</span></span>

<span data-ttu-id="9ff29-233">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="9ff29-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="9ff29-234">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="9ff29-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="9ff29-235">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="9ff29-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="9ff29-236">Wsuń usługę autoryzacji w pliku *Pages/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="9ff29-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="9ff29-237">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="9ff29-238">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="9ff29-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="9ff29-239">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="9ff29-240">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="9ff29-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="9ff29-241">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="9ff29-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="9ff29-242">Strona Razor lub kontroler musi wymusić sprawdzanie dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="9ff29-243">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-243">Update Details</span></span>

<span data-ttu-id="9ff29-244">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="9ff29-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="9ff29-245">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="9ff29-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="9ff29-246">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="9ff29-246">Add or remove a user to a role</span></span>

<span data-ttu-id="9ff29-247">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="9ff29-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="9ff29-248">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-248">Removing privileges from a user.</span></span> <span data-ttu-id="9ff29-249">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="9ff29-250">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="9ff29-251">Różnice między wyzwaniem i Zabroń</span><span class="sxs-lookup"><span data-stu-id="9ff29-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="9ff29-252">Ta aplikacja ustawia zasady domyślne, aby [wymagać uwierzytelnionych użytkowników](#require-authenticated-users).</span><span class="sxs-lookup"><span data-stu-id="9ff29-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="9ff29-253">Poniższy kod umożliwia anonimowym użytkownikom.</span><span class="sxs-lookup"><span data-stu-id="9ff29-253">The following code allows anonymous users.</span></span> <span data-ttu-id="9ff29-254">Użytkownicy anonimowi mogą wyświetlać różnice między wyzwaniem a zabranianiem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="9ff29-255">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9ff29-255">In the preceding code:</span></span>

* <span data-ttu-id="9ff29-256">Jeśli użytkownik **nie** jest uwierzytelniony, zwracany jest `ChallengeResult` element.</span><span class="sxs-lookup"><span data-stu-id="9ff29-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="9ff29-257">Gdy `ChallengeResult` jest zwracany, użytkownik zostanie przekierowany do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="9ff29-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="9ff29-258">Gdy użytkownik jest uwierzytelniany, ale nie jest autoryzowany `ForbidResult` , zwracany jest element.</span><span class="sxs-lookup"><span data-stu-id="9ff29-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="9ff29-259">Gdy `ForbidResult` jest zwracany, użytkownik zostanie przekierowany do strony odmowa dostępu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="9ff29-260">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-260">Test the completed app</span></span>

<span data-ttu-id="9ff29-261">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="9ff29-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="9ff29-262">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="9ff29-263">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="9ff29-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="9ff29-264">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="9ff29-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="9ff29-265">Jeśli aplikacja ma kontakty:</span><span class="sxs-lookup"><span data-stu-id="9ff29-265">If the app has contacts:</span></span>

* <span data-ttu-id="9ff29-266">Usuń wszystkie rekordy z `Contact` tabeli.</span><span class="sxs-lookup"><span data-stu-id="9ff29-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="9ff29-267">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="9ff29-268">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="9ff29-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="9ff29-269">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="9ff29-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="9ff29-270">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="9ff29-271">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="9ff29-271">Verify the following operations:</span></span>

* <span data-ttu-id="9ff29-272">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="9ff29-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="9ff29-273">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="9ff29-274">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="9ff29-275">`Details` Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="9ff29-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="9ff29-276">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="9ff29-277">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="9ff29-277">User</span></span>                | <span data-ttu-id="9ff29-278">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="9ff29-278">Seeded by the app</span></span> | <span data-ttu-id="9ff29-279">Opcje</span><span class="sxs-lookup"><span data-stu-id="9ff29-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="9ff29-280">Nie</span><span class="sxs-lookup"><span data-stu-id="9ff29-280">No</span></span>                | <span data-ttu-id="9ff29-281">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="9ff29-282">Tak</span><span class="sxs-lookup"><span data-stu-id="9ff29-282">Yes</span></span>               | <span data-ttu-id="9ff29-283">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="9ff29-284">Tak</span><span class="sxs-lookup"><span data-stu-id="9ff29-284">Yes</span></span>               | <span data-ttu-id="9ff29-285">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="9ff29-286">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="9ff29-287">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="9ff29-288">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="9ff29-289">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="9ff29-289">Create the starter app</span></span>

* <span data-ttu-id="9ff29-290">Tworzenie aplikacji Razor Pages o nazwie "Contacter"</span><span class="sxs-lookup"><span data-stu-id="9ff29-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="9ff29-291">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="9ff29-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="9ff29-292">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="9ff29-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="9ff29-293">`-uld`Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="9ff29-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="9ff29-294">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ff29-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="9ff29-295">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="9ff29-296">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="9ff29-297">Jeśli wystąpi usterka z `dotnet aspnet-codegenerator razorpage` poleceniem, zobacz [ten problem](https://github.com/aspnet/Scaffolding/issues/984)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="9ff29-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="9ff29-298">Zaktualizuj kotwicę **ContactManager** w pliku *Pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9ff29-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="9ff29-299">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="9ff29-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="9ff29-300">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="9ff29-300">Seed the database</span></span>

<span data-ttu-id="9ff29-301">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) do folderu *danych* :</span><span class="sxs-lookup"><span data-stu-id="9ff29-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="9ff29-302">Wywołanie `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="9ff29-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="9ff29-303">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-303">Test that the app seeded the database.</span></span> <span data-ttu-id="9ff29-304">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="9ff29-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="9ff29-305">W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację.</span><span class="sxs-lookup"><span data-stu-id="9ff29-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="9ff29-306">Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="9ff29-307">Istnieją trzy grupy zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="9ff29-307">There are three security groups:</span></span>

* <span data-ttu-id="9ff29-308">**Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="9ff29-309">**Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="9ff29-310">Tylko zatwierdzone kontakty są widoczne dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="9ff29-311">**Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="9ff29-312">Na poniższej ilustracji użytkownik Rick (`rick@example.com`) jest zalogowany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="9ff29-313">Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować**/**Usuń**/**Utwórz nowe** linki dla swoich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="9ff29-314">Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** .</span><span class="sxs-lookup"><span data-stu-id="9ff29-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="9ff29-315">Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

<span data-ttu-id="9ff29-317">Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:</span><span class="sxs-lookup"><span data-stu-id="9ff29-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Zrzut ekranu manager@contoso.com przedstawiający zalogowany](secure-data/_static/manager1.png)

<span data-ttu-id="9ff29-319">Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:</span><span class="sxs-lookup"><span data-stu-id="9ff29-319">The following image shows the managers details view of a contact:</span></span>

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

<span data-ttu-id="9ff29-321">Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="9ff29-322">Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:</span><span class="sxs-lookup"><span data-stu-id="9ff29-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Zrzut ekranu admin@contoso.com przedstawiający zalogowany](secure-data/_static/admin.png)

<span data-ttu-id="9ff29-324">Administrator ma wszystkie uprawnienia.</span><span class="sxs-lookup"><span data-stu-id="9ff29-324">The administrator has all privileges.</span></span> <span data-ttu-id="9ff29-325">Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="9ff29-326">Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="9ff29-327">Przykład zawiera następujące programy obsługi autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="9ff29-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="9ff29-328">`ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="9ff29-329">`ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="9ff29-330">`ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ff29-331">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9ff29-331">Prerequisites</span></span>

<span data-ttu-id="9ff29-332">Ten samouczek jest zaawansowany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-332">This tutorial is advanced.</span></span> <span data-ttu-id="9ff29-333">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="9ff29-333">You should be familiar with:</span></span>

* [<span data-ttu-id="9ff29-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ff29-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="9ff29-335">Authentication</span><span class="sxs-lookup"><span data-stu-id="9ff29-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="9ff29-336">Potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="9ff29-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="9ff29-337">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="9ff29-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="9ff29-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9ff29-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="9ff29-339">Aplikacja Starter i ukończona</span><span class="sxs-lookup"><span data-stu-id="9ff29-339">The starter and completed app</span></span>

<span data-ttu-id="9ff29-340">[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację.</span><span class="sxs-lookup"><span data-stu-id="9ff29-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="9ff29-341">[Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="9ff29-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="9ff29-342">Aplikacja początkowa</span><span class="sxs-lookup"><span data-stu-id="9ff29-342">The starter app</span></span>

<span data-ttu-id="9ff29-343">[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="9ff29-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="9ff29-344">Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="9ff29-345">Zabezpieczanie danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="9ff29-345">Secure user data</span></span>

<span data-ttu-id="9ff29-346">Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="9ff29-347">Pomocne może być odwołanie do ukończonego projektu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="9ff29-348">Powiązanie danych kontaktowych z użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="9ff29-348">Tie the contact data to the user</span></span>

<span data-ttu-id="9ff29-349">Użyj identyfikatora użytkownika [tożsamości](xref:security/authentication/identity) ASP.NET, aby upewnić się, że użytkownicy będą mogli edytować swoje dane, ale nie inne dane użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9ff29-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="9ff29-350">Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="9ff29-351">`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w bazie danych [tożsamości](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="9ff29-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="9ff29-352">Pole `Status` określa, czy kontakt jest widoczny dla użytkowników ogólnych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="9ff29-353">Utwórz nową migrację i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="9ff29-354">Dodawanie usług ról do tożsamości</span><span class="sxs-lookup"><span data-stu-id="9ff29-354">Add Role services to Identity</span></span>

<span data-ttu-id="9ff29-355">Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:</span><span class="sxs-lookup"><span data-stu-id="9ff29-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="9ff29-356">Wymagaj uwierzytelnionych użytkowników</span><span class="sxs-lookup"><span data-stu-id="9ff29-356">Require authenticated users</span></span>

<span data-ttu-id="9ff29-357">Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:</span><span class="sxs-lookup"><span data-stu-id="9ff29-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="9ff29-358">Można zrezygnować z uwierzytelniania na stronie Razor, kontrolerze lub poziomie metody akcji przy użyciu `[AllowAnonymous]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="9ff29-359">Ustawienie domyślnych zasad uwierzytelniania wymagające uwierzytelnienia użytkowników chroniących nowo dodane Razor Pages i kontrolery.</span><span class="sxs-lookup"><span data-stu-id="9ff29-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="9ff29-360">Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i Razor Pages uwzględnienia `[Authorize]` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="9ff29-361">Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do strony indeks, informacje i kontakty, dzięki czemu anonimowi użytkownicy mogą uzyskać informacje o witrynie przed ich zarejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="9ff29-362">Konfigurowanie konta testowego</span><span class="sxs-lookup"><span data-stu-id="9ff29-362">Configure the test account</span></span>

<span data-ttu-id="9ff29-363">`SeedData` Klasa tworzy dwa konta: administrator i Menedżer.</span><span class="sxs-lookup"><span data-stu-id="9ff29-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="9ff29-364">Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont.</span><span class="sxs-lookup"><span data-stu-id="9ff29-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="9ff29-365">Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):</span><span class="sxs-lookup"><span data-stu-id="9ff29-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="9ff29-366">Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="9ff29-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="9ff29-367">Zaktualizuj `Main` , aby użyć hasła testu:</span><span class="sxs-lookup"><span data-stu-id="9ff29-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="9ff29-368">Tworzenie kont testowych i aktualizowanie kontaktów</span><span class="sxs-lookup"><span data-stu-id="9ff29-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="9ff29-369">Zaktualizuj `Initialize` metodę w `SeedData` klasie, aby utworzyć konta testowe:</span><span class="sxs-lookup"><span data-stu-id="9ff29-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="9ff29-370">Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="9ff29-371">Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony".</span><span class="sxs-lookup"><span data-stu-id="9ff29-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="9ff29-372">Dodaj identyfikator i stan użytkownika do wszystkich kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="9ff29-373">Pokazywany jest tylko jeden kontakt:</span><span class="sxs-lookup"><span data-stu-id="9ff29-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="9ff29-374">Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora</span><span class="sxs-lookup"><span data-stu-id="9ff29-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="9ff29-375">Utwórz folder *autoryzacji* i Utwórz w nim `ContactIsOwnerAuthorizationHandler` klasę.</span><span class="sxs-lookup"><span data-stu-id="9ff29-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="9ff29-376">`ContactIsOwnerAuthorizationHandler` Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="9ff29-377">Kontekst `ContactIsOwnerAuthorizationHandler` wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="9ff29-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="9ff29-378">Obsługa autoryzacji zazwyczaj:</span><span class="sxs-lookup"><span data-stu-id="9ff29-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="9ff29-379">Zwróć `context.Succeed` , gdy wymagania są spełnione.</span><span class="sxs-lookup"><span data-stu-id="9ff29-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="9ff29-380">Zwracaj `Task.CompletedTask` , gdy wymagania nie są spełnione.</span><span class="sxs-lookup"><span data-stu-id="9ff29-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="9ff29-381">`Task.CompletedTask`nie powiodło się lub&mdash;niepowodzenie — zezwala na uruchamianie innych programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="9ff29-382">Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="9ff29-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="9ff29-383">Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="9ff29-384">`ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.</span><span class="sxs-lookup"><span data-stu-id="9ff29-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="9ff29-385">Tworzenie procedury obsługi autoryzacji Menedżera</span><span class="sxs-lookup"><span data-stu-id="9ff29-385">Create a manager authorization handler</span></span>

<span data-ttu-id="9ff29-386">Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="9ff29-387">`ContactManagerAuthorizationHandler` Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="9ff29-388">Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).</span><span class="sxs-lookup"><span data-stu-id="9ff29-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="9ff29-389">Tworzenie procedury obsługi autoryzacji administratora</span><span class="sxs-lookup"><span data-stu-id="9ff29-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="9ff29-390">Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="9ff29-391">`ContactAdministratorsAuthorizationHandler` Sprawdza, czy użytkownik działający na tym zasobie jest administratorem.</span><span class="sxs-lookup"><span data-stu-id="9ff29-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="9ff29-392">Administrator może wykonać wszystkie operacje.</span><span class="sxs-lookup"><span data-stu-id="9ff29-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="9ff29-393">Rejestrowanie programów obsługi autoryzacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-393">Register the authorization handlers</span></span>

<span data-ttu-id="9ff29-394">Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="9ff29-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="9ff29-395">`ContactIsOwnerAuthorizationHandler` Używa ASP.NET Core [tożsamość](xref:security/authentication/identity), która jest oparta na Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="9ff29-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="9ff29-396">Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez.</span><span class="sxs-lookup"><span data-stu-id="9ff29-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9ff29-397">Dodaj następujący kod na końcu `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ff29-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="9ff29-398">`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze.</span><span class="sxs-lookup"><span data-stu-id="9ff29-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="9ff29-399">Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują `Context` się w parametrze `HandleRequirementAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="9ff29-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="9ff29-400">Obsługa autoryzacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-400">Support authorization</span></span>

<span data-ttu-id="9ff29-401">W tej sekcji należy zaktualizować Razor Pages i dodać klasę wymagania operacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="9ff29-402">Przeglądanie klasy wymagań operacji kontaktu</span><span class="sxs-lookup"><span data-stu-id="9ff29-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="9ff29-403">Zapoznaj `ContactOperations` się z klasą.</span><span class="sxs-lookup"><span data-stu-id="9ff29-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="9ff29-404">Ta klasa zawiera wymagania obsługiwane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="9ff29-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="9ff29-405">Utwórz klasę bazową dla kontaktów Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9ff29-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="9ff29-406">Utwórz klasę bazową zawierającą usługi używane w Razor Pages kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="9ff29-407">Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="9ff29-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="9ff29-408">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="9ff29-408">The preceding code:</span></span>

* <span data-ttu-id="9ff29-409">Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="9ff29-410">Dodaje usługę tożsamości `UserManager` .</span><span class="sxs-lookup"><span data-stu-id="9ff29-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="9ff29-411">Dodaj `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="9ff29-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="9ff29-412">Aktualizowanie modelu</span><span class="sxs-lookup"><span data-stu-id="9ff29-412">Update the CreateModel</span></span>

<span data-ttu-id="9ff29-413">Zaktualizuj konstruktora Create Page model, aby używał klasy `DI_BasePageModel` bazowej:</span><span class="sxs-lookup"><span data-stu-id="9ff29-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="9ff29-414">Zaktualizuj `CreateModel.OnPostAsync` metodę do:</span><span class="sxs-lookup"><span data-stu-id="9ff29-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="9ff29-415">Dodaj identyfikator użytkownika do `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="9ff29-416">Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="9ff29-417">Aktualizowanie IndexModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-417">Update the IndexModel</span></span>

<span data-ttu-id="9ff29-418">Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="9ff29-419">Aktualizowanie EditModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-419">Update the EditModel</span></span>

<span data-ttu-id="9ff29-420">Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej.</span><span class="sxs-lookup"><span data-stu-id="9ff29-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="9ff29-421">Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały.</span><span class="sxs-lookup"><span data-stu-id="9ff29-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="9ff29-422">Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty.</span><span class="sxs-lookup"><span data-stu-id="9ff29-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="9ff29-423">Autoryzacja na podstawie zasobów musi być bezwzględna.</span><span class="sxs-lookup"><span data-stu-id="9ff29-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="9ff29-424">Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi.</span><span class="sxs-lookup"><span data-stu-id="9ff29-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="9ff29-425">Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="9ff29-426">Aktualizowanie DeleteModel</span><span class="sxs-lookup"><span data-stu-id="9ff29-426">Update the DeleteModel</span></span>

<span data-ttu-id="9ff29-427">Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.</span><span class="sxs-lookup"><span data-stu-id="9ff29-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="9ff29-428">Wsuń usługę autoryzacji do widoków</span><span class="sxs-lookup"><span data-stu-id="9ff29-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="9ff29-429">Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.</span><span class="sxs-lookup"><span data-stu-id="9ff29-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="9ff29-430">Wsuń usługę autoryzacji w pliku *views/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:</span><span class="sxs-lookup"><span data-stu-id="9ff29-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="9ff29-431">Powyższy znacznik dodaje kilka `using` instrukcji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="9ff29-432">Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:</span><span class="sxs-lookup"><span data-stu-id="9ff29-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="9ff29-433">Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="9ff29-434">Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki.</span><span class="sxs-lookup"><span data-stu-id="9ff29-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="9ff29-435">Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami.</span><span class="sxs-lookup"><span data-stu-id="9ff29-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="9ff29-436">Strona Razor lub kontroler musi wymusić sprawdzanie dostępu w celu zabezpieczenia danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="9ff29-437">Szczegóły aktualizacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-437">Update Details</span></span>

<span data-ttu-id="9ff29-438">Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:</span><span class="sxs-lookup"><span data-stu-id="9ff29-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="9ff29-439">Zaktualizuj model strony szczegółów:</span><span class="sxs-lookup"><span data-stu-id="9ff29-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="9ff29-440">Dodawanie użytkownika do roli lub usuwanie go</span><span class="sxs-lookup"><span data-stu-id="9ff29-440">Add or remove a user to a role</span></span>

<span data-ttu-id="9ff29-441">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:</span><span class="sxs-lookup"><span data-stu-id="9ff29-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="9ff29-442">Usuwanie uprawnień użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-442">Removing privileges from a user.</span></span> <span data-ttu-id="9ff29-443">Na przykład wyciszenie użytkownika w aplikacji czatu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="9ff29-444">Dodawanie uprawnień do użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="9ff29-445">Testowanie ukończonej aplikacji</span><span class="sxs-lookup"><span data-stu-id="9ff29-445">Test the completed app</span></span>

<span data-ttu-id="9ff29-446">Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:</span><span class="sxs-lookup"><span data-stu-id="9ff29-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="9ff29-447">Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="9ff29-448">Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.</span><span class="sxs-lookup"><span data-stu-id="9ff29-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="9ff29-449">Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:</span><span class="sxs-lookup"><span data-stu-id="9ff29-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="9ff29-450">Porzuć i zaktualizuj bazę danych</span><span class="sxs-lookup"><span data-stu-id="9ff29-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="9ff29-451">Uruchom ponownie aplikację, aby wypełniać bazę danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="9ff29-452">Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="9ff29-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="9ff29-453">W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="9ff29-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="9ff29-454">Zaloguj się do każdej przeglądarki za pomocą innego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9ff29-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="9ff29-455">Sprawdź następujące operacje:</span><span class="sxs-lookup"><span data-stu-id="9ff29-455">Verify the following operations:</span></span>

* <span data-ttu-id="9ff29-456">Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.</span><span class="sxs-lookup"><span data-stu-id="9ff29-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="9ff29-457">Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="9ff29-458">Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów.</span><span class="sxs-lookup"><span data-stu-id="9ff29-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="9ff29-459">`Details` Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .</span><span class="sxs-lookup"><span data-stu-id="9ff29-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="9ff29-460">Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="9ff29-461">Użytkownik</span><span class="sxs-lookup"><span data-stu-id="9ff29-461">User</span></span>                | <span data-ttu-id="9ff29-462">Wypełnianie przez aplikację</span><span class="sxs-lookup"><span data-stu-id="9ff29-462">Seeded by the app</span></span> | <span data-ttu-id="9ff29-463">Opcje</span><span class="sxs-lookup"><span data-stu-id="9ff29-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="9ff29-464">Nie</span><span class="sxs-lookup"><span data-stu-id="9ff29-464">No</span></span>                | <span data-ttu-id="9ff29-465">Edytuj/Usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="9ff29-466">Tak</span><span class="sxs-lookup"><span data-stu-id="9ff29-466">Yes</span></span>               | <span data-ttu-id="9ff29-467">Zatwierdź/Odrzuć i edytuj/usuń własne dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="9ff29-468">Tak</span><span class="sxs-lookup"><span data-stu-id="9ff29-468">Yes</span></span>               | <span data-ttu-id="9ff29-469">Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane.</span><span class="sxs-lookup"><span data-stu-id="9ff29-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="9ff29-470">Utwórz kontakt w przeglądarce administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="9ff29-471">Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora.</span><span class="sxs-lookup"><span data-stu-id="9ff29-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="9ff29-472">Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.</span><span class="sxs-lookup"><span data-stu-id="9ff29-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="9ff29-473">Tworzenie aplikacji Starter</span><span class="sxs-lookup"><span data-stu-id="9ff29-473">Create the starter app</span></span>

* <span data-ttu-id="9ff29-474">Tworzenie aplikacji Razor stronicowej o nazwie "contacter"</span><span class="sxs-lookup"><span data-stu-id="9ff29-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="9ff29-475">Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="9ff29-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="9ff29-476">Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.</span><span class="sxs-lookup"><span data-stu-id="9ff29-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="9ff29-477">`-uld`Określa LocalDB zamiast oprogramowania SQLite</span><span class="sxs-lookup"><span data-stu-id="9ff29-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="9ff29-478">Dodaj *modele/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="9ff29-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="9ff29-479">Tworzenie szkieletu `Contact` modelu.</span><span class="sxs-lookup"><span data-stu-id="9ff29-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="9ff29-480">Utwórz migrację początkową i zaktualizuj bazę danych:</span><span class="sxs-lookup"><span data-stu-id="9ff29-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="9ff29-481">Zaktualizuj kotwicę **ContactManager** w pliku *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9ff29-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="9ff29-482">Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu</span><span class="sxs-lookup"><span data-stu-id="9ff29-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="9ff29-483">Wypełnianie bazy danych</span><span class="sxs-lookup"><span data-stu-id="9ff29-483">Seed the database</span></span>

<span data-ttu-id="9ff29-484">Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) do folderu *danych* .</span><span class="sxs-lookup"><span data-stu-id="9ff29-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="9ff29-485">Wywołanie `SeedData.Initialize` z `Main`:</span><span class="sxs-lookup"><span data-stu-id="9ff29-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="9ff29-486">Sprawdź, czy aplikacja wykorzystana z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="9ff29-486">Test that the app seeded the database.</span></span> <span data-ttu-id="9ff29-487">Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="9ff29-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="9ff29-488">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9ff29-488">Additional resources</span></span>

* [<span data-ttu-id="9ff29-489">Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9ff29-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="9ff29-490">[ASP.NET Core laboratorium autoryzacji](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="9ff29-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="9ff29-491">To laboratorium prowadzi do bardziej szczegółowych informacji na temat funkcji zabezpieczeń wprowadzonych w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="9ff29-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="9ff29-492">Niestandardowa Autoryzacja oparta na zasadach</span><span class="sxs-lookup"><span data-stu-id="9ff29-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
