---
title: Wprowadzenie do Identity ASP.NET Core
author: rick-anderson
description: Używanie Identity z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 25070e90050db9dca8b003ae782662811096526a
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160310"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="d070b-104">Wprowadzenie do Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d070b-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d070b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d070b-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="d070b-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d070b-107">Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d070b-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="d070b-108">Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.</span><span class="sxs-lookup"><span data-stu-id="d070b-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="d070b-109">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="d070b-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d070b-110">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d070b-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="d070b-111">[ Identity Kod źródłowy](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) jest dostępny w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="d070b-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="d070b-112">[Szkielet Identity ](xref:security/authentication/scaffold-identity) i Wyświetl wygenerowane pliki w celu przejrzenia interakcji z szablonem Identity .</span><span class="sxs-lookup"><span data-stu-id="d070b-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="d070b-113">Identityjest zazwyczaj konfigurowany przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="d070b-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d070b-114">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="d070b-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d070b-115">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d070b-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d070b-116">Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="d070b-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="d070b-117">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz [następne kroki](#next).</span><span class="sxs-lookup"><span data-stu-id="d070b-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="d070b-118">[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:</span><span class="sxs-lookup"><span data-stu-id="d070b-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="d070b-119">Ewolucja platformy deweloperów Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="d070b-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="d070b-120">Niepowiązane z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="d070b-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="d070b-121">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d070b-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d070b-122">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="d070b-122">Create a Web app with authentication</span></span>

<span data-ttu-id="d070b-123">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="d070b-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d070b-125">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d070b-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d070b-126">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="d070b-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d070b-127">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="d070b-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d070b-128">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="d070b-128">Click **OK**.</span></span>
* <span data-ttu-id="d070b-129">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="d070b-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d070b-130">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="d070b-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-131">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="d070b-132">Poprzednie polecenie tworzy Razor aplikację internetową przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="d070b-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="d070b-133">Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d070b-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="d070b-134">Wygenerowany projekt zawiera [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d070b-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d070b-135">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="d070b-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d070b-136">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d070b-136">For example:</span></span>

* <span data-ttu-id="d070b-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d070b-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="d070b-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d070b-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d070b-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d070b-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d070b-140">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="d070b-140">Apply migrations</span></span>

<span data-ttu-id="d070b-141">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="d070b-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d070b-143">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="d070b-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-144">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d070b-145">W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="d070b-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d070b-146">W przypadku LocalDB Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d070b-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d070b-147">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="d070b-147">Test Register and Login</span></span>

<span data-ttu-id="d070b-148">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d070b-148">Run the app and register a user.</span></span> <span data-ttu-id="d070b-149">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="d070b-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="d070b-150">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="d070b-150">Configure Identity services</span></span>

<span data-ttu-id="d070b-151">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d070b-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d070b-152">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="d070b-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="d070b-153">Poprzedni wyróżniony kod konfiguruje Identity domyślne wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="d070b-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="d070b-154">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d070b-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d070b-155">Identityjest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="d070b-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="d070b-156">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="d070b-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="d070b-157">Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="d070b-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="d070b-158">`app.UseAuthorization`jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację.</span><span class="sxs-lookup"><span data-stu-id="d070b-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="d070b-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="d070b-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="d070b-160">Aby uzyskać więcej informacji na temat programów `IdentityOptions` i `Startup` , zobacz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d070b-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="d070b-161">Rejestrowanie szkieletów, logowanie, wylogowywanie i RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="d070b-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d070b-163">Dodaj `Register` pliki, `Login` , `LogOut` i `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="d070b-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="d070b-164">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="d070b-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-165">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d070b-166">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="d070b-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d070b-167">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d070b-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="d070b-168">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="d070b-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d070b-169">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d070b-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="d070b-170">Aby uzyskać więcej informacji na temat tworzenia szkieletów Identity , zobacz [tożsamość szkieletu w Razor projekcie z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d070b-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d070b-171">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="d070b-171">Examine Register</span></span>

<span data-ttu-id="d070b-172">Gdy użytkownik kliknie przycisk **zarejestruj** na `Register` stronie, `RegisterModel.OnPostAsync` Akcja jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d070b-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d070b-173">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="d070b-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="d070b-174">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="d070b-174">Log in</span></span>

<span data-ttu-id="d070b-175">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="d070b-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="d070b-176">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="d070b-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d070b-177">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="d070b-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d070b-178">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="d070b-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d070b-179">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="d070b-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d070b-180">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="d070b-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d070b-181">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="d070b-181">Log out</span></span>

<span data-ttu-id="d070b-182">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="d070b-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="d070b-183">W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="d070b-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="d070b-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="d070b-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d070b-185">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d070b-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="d070b-186">BadanIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-186">Test Identity</span></span>

<span data-ttu-id="d070b-187">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="d070b-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d070b-188">Aby przetestować Identity , Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d070b-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d070b-189">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="d070b-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d070b-190">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="d070b-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="d070b-191">PrzeglądaćIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-191">Explore Identity</span></span>

<span data-ttu-id="d070b-192">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="d070b-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d070b-193">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="d070b-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d070b-194">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="d070b-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="d070b-195">IdentityKomponentów</span><span class="sxs-lookup"><span data-stu-id="d070b-195">Identity Components</span></span>

<span data-ttu-id="d070b-196">Wszystkie Identity zależne pakiety NuGet są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="d070b-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="d070b-197">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="d070b-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d070b-198">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d070b-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="d070b-199">Migrowanie do ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d070b-200">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d070b-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d070b-201">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="d070b-201">Setting password strength</span></span>

<span data-ttu-id="d070b-202">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="d070b-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="d070b-203">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d070b-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d070b-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d070b-205">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="d070b-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d070b-206">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d070b-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="d070b-207">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="d070b-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d070b-208">Aby uniemożliwić publikowanie statycznych Identity zasobów (arkuszy stylów i plików JavaScript dla Identity interfejsu użytkownika) w katalogu głównym sieci Web, Dodaj następującą `ResolveStaticWebAssetsInputsDependsOn` Właściwość i `RemoveIdentityAssets` obiekt docelowy do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d070b-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="d070b-209">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="d070b-209">Next Steps</span></span>

* <span data-ttu-id="d070b-210">[ASP.NET Core Identity kod źródłowy](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="d070b-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="d070b-211">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="d070b-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d070b-212">PonowneIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d070b-213">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d070b-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d070b-214">ASP.NET Core Identity to system członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d070b-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="d070b-215">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="d070b-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d070b-216">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d070b-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d070b-217">Identitymożna skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="d070b-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d070b-218">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="d070b-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d070b-219">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d070b-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d070b-220">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d070b-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d070b-221">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="d070b-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="d070b-222">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d070b-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d070b-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d070b-224">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="d070b-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d070b-225">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d070b-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d070b-226">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="d070b-226">Create a Web app with authentication</span></span>

<span data-ttu-id="d070b-227">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="d070b-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d070b-229">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d070b-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d070b-230">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="d070b-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d070b-231">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="d070b-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d070b-232">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="d070b-232">Click **OK**.</span></span>
* <span data-ttu-id="d070b-233">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="d070b-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d070b-234">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="d070b-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-235">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="d070b-236">Wygenerowany projekt zawiera [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d070b-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d070b-237">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="d070b-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d070b-238">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d070b-238">For example:</span></span>

* <span data-ttu-id="d070b-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d070b-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="d070b-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d070b-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d070b-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d070b-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d070b-242">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="d070b-242">Apply migrations</span></span>

<span data-ttu-id="d070b-243">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="d070b-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d070b-245">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="d070b-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-246">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d070b-247">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="d070b-247">Test Register and Login</span></span>

<span data-ttu-id="d070b-248">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d070b-248">Run the app and register a user.</span></span> <span data-ttu-id="d070b-249">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="d070b-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="d070b-250">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="d070b-250">Configure Identity services</span></span>

<span data-ttu-id="d070b-251">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d070b-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d070b-252">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="d070b-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="d070b-253">Poprzedni kod konfiguruje Identity z użyciem domyślnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="d070b-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d070b-254">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d070b-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d070b-255">Identityjest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d070b-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d070b-256">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="d070b-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="d070b-257">Aby uzyskać więcej informacji, zobacz [ Identity klasy opcji](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d070b-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d070b-258">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="d070b-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="d070b-259">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="d070b-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d070b-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d070b-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d070b-261">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="d070b-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d070b-262">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d070b-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d070b-263">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="d070b-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d070b-264">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d070b-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d070b-265">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="d070b-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d070b-266">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="d070b-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d070b-267">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="d070b-267">Examine Register</span></span>

<span data-ttu-id="d070b-268">Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="d070b-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d070b-269">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="d070b-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="d070b-270">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="d070b-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d070b-271">**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="d070b-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d070b-272">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="d070b-272">Log in</span></span>

<span data-ttu-id="d070b-273">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="d070b-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="d070b-274">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="d070b-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d070b-275">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="d070b-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d070b-276">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="d070b-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d070b-277">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="d070b-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d070b-278">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="d070b-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d070b-279">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="d070b-279">Log out</span></span>

<span data-ttu-id="d070b-280">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="d070b-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="d070b-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="d070b-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d070b-282">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d070b-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="d070b-283">BadanIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-283">Test Identity</span></span>

<span data-ttu-id="d070b-284">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="d070b-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d070b-285">Aby przetestować Identity , Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.</span><span class="sxs-lookup"><span data-stu-id="d070b-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d070b-286">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="d070b-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d070b-287">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="d070b-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="d070b-288">PrzeglądaćIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-288">Explore Identity</span></span>

<span data-ttu-id="d070b-289">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="d070b-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d070b-290">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="d070b-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d070b-291">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="d070b-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="d070b-292">IdentityKomponentów</span><span class="sxs-lookup"><span data-stu-id="d070b-292">Identity Components</span></span>

<span data-ttu-id="d070b-293">Wszystkie Identity zależne pakiety NuGet są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d070b-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d070b-294">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="d070b-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d070b-295">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d070b-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="d070b-296">Migrowanie do ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d070b-297">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d070b-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d070b-298">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="d070b-298">Setting password strength</span></span>

<span data-ttu-id="d070b-299">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="d070b-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d070b-300">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="d070b-300">Next Steps</span></span>

* <span data-ttu-id="d070b-301">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="d070b-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d070b-302">PonowneIdentity</span><span class="sxs-lookup"><span data-stu-id="d070b-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
