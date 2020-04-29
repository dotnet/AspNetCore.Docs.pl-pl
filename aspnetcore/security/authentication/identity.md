---
title: Wprowadzenie do tożsamości na ASP.NET Core
author: rick-anderson
description: Użyj tożsamości z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205946"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="cdcac-104">Wprowadzenie do tożsamości na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdcac-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdcac-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cdcac-106">ASP.NET Core tożsamość:</span><span class="sxs-lookup"><span data-stu-id="cdcac-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cdcac-107">Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cdcac-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="cdcac-108">Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.</span><span class="sxs-lookup"><span data-stu-id="cdcac-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="cdcac-109">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w tożsamości lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cdcac-110">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cdcac-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cdcac-111">[Kod źródłowy tożsamości](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) jest dostępny w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="cdcac-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="cdcac-112">[Tożsamość szkieletu](xref:security/authentication/scaffold-identity) i wyświetlanie wygenerowanych plików w celu przejrzenia interakcji szablonu z tożsamością.</span><span class="sxs-lookup"><span data-stu-id="cdcac-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="cdcac-113">Tożsamość jest zazwyczaj konfigurowana przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cdcac-114">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cdcac-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cdcac-115">W tym temacie dowiesz się, jak używać tożsamości do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cdcac-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cdcac-116">Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cdcac-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="cdcac-117">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji korzystających z tożsamości, zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="cdcac-118">[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:</span><span class="sxs-lookup"><span data-stu-id="cdcac-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="cdcac-119">Ewolucja platformy deweloperów Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="cdcac-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="cdcac-120">Niepowiązane z tożsamością ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdcac-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="cdcac-121">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdcac-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cdcac-122">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="cdcac-122">Create a Web app with authentication</span></span>

<span data-ttu-id="cdcac-123">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cdcac-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdcac-125">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cdcac-126">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cdcac-127">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cdcac-128">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-128">Click **OK**.</span></span>
* <span data-ttu-id="cdcac-129">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cdcac-130">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-131">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="cdcac-132">Poprzednie polecenie tworzy aplikację sieci Web Razor przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdcac-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="cdcac-133">Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cdcac-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="cdcac-134">Wygenerowany projekt zapewnia [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cdcac-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cdcac-135">Biblioteka klas Razor tożsamość ujawnia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="cdcac-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cdcac-136">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cdcac-136">For example:</span></span>

* <span data-ttu-id="cdcac-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cdcac-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="cdcac-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cdcac-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cdcac-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cdcac-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cdcac-140">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="cdcac-140">Apply migrations</span></span>

<span data-ttu-id="cdcac-141">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cdcac-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdcac-143">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="cdcac-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-144">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdcac-145">W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="cdcac-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="cdcac-146">W przypadku LocalDB Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cdcac-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cdcac-147">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="cdcac-147">Test Register and Login</span></span>

<span data-ttu-id="cdcac-148">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cdcac-148">Run the app and register a user.</span></span> <span data-ttu-id="cdcac-149">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cdcac-150">Konfigurowanie usług tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-150">Configure Identity services</span></span>

<span data-ttu-id="cdcac-151">Usługi są dodawane do `ConfigureServices`programu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cdcac-152">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="cdcac-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="cdcac-153">Poprzedni wyróżniony kod konfiguruje tożsamość z domyślnymi wartościami opcji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="cdcac-154">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdcac-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cdcac-155">Tożsamość jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="cdcac-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="cdcac-156">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="cdcac-157">Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="cdcac-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="cdcac-158">`app.UseAuthorization`jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację.</span><span class="sxs-lookup"><span data-stu-id="cdcac-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="cdcac-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="cdcac-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="cdcac-160">Aby uzyskać więcej informacji `IdentityOptions` na `Startup`temat programów <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i, zobacz i [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cdcac-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cdcac-161">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="cdcac-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdcac-163">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="cdcac-164">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w projekcie Razor z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-165">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdcac-166">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="cdcac-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cdcac-167">W przeciwnym razie użyj prawidłowej przestrzeni nazw `ApplicationDbContext`dla:</span><span class="sxs-lookup"><span data-stu-id="cdcac-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cdcac-168">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="cdcac-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cdcac-169">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="cdcac-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="cdcac-170">Aby uzyskać więcej informacji na temat tworzenia szkieletów tożsamości, zobacz [tożsamość szkieletu w projekcie Razor z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="cdcac-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cdcac-171">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="cdcac-171">Examine Register</span></span>

<span data-ttu-id="cdcac-172">Gdy użytkownik kliknie łącze **zarejestruj** , zostanie wywołana `RegisterModel.OnPostAsync` akcja.</span><span class="sxs-lookup"><span data-stu-id="cdcac-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cdcac-173">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) dla `_userManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="cdcac-174">`_userManager`jest dostarczany przez wstrzyknięcie zależności):</span><span class="sxs-lookup"><span data-stu-id="cdcac-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="cdcac-175">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="cdcac-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cdcac-176">Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cdcac-177">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="cdcac-177">Log in</span></span>

<span data-ttu-id="cdcac-178">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="cdcac-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="cdcac-179">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cdcac-180">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="cdcac-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cdcac-181">Po przesłaniu formularza na stronie logowania zostanie wywołana `OnPostAsync` akcja.</span><span class="sxs-lookup"><span data-stu-id="cdcac-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cdcac-182">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu (dostarczone przez iniekcję zależności).</span><span class="sxs-lookup"><span data-stu-id="cdcac-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cdcac-183">Klasa bazowa `Controller` uwidacznia `User` właściwość, do której można uzyskać dostęp z metod kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cdcac-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="cdcac-184">Na przykład można wyliczyć `User.Claims` i podjąć decyzje dotyczące autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="cdcac-185">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="cdcac-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cdcac-186">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="cdcac-186">Log out</span></span>

<span data-ttu-id="cdcac-187">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="cdcac-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="cdcac-188">W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="cdcac-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="cdcac-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="cdcac-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cdcac-190">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cdcac-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="cdcac-191">Testuj tożsamość</span><span class="sxs-lookup"><span data-stu-id="cdcac-191">Test Identity</span></span>

<span data-ttu-id="cdcac-192">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="cdcac-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cdcac-193">Aby przetestować tożsamość, Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="cdcac-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cdcac-194">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cdcac-195">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cdcac-196">Eksploruj tożsamość</span><span class="sxs-lookup"><span data-stu-id="cdcac-196">Explore Identity</span></span>

<span data-ttu-id="cdcac-197">Aby poznać tożsamość w bardziej szczegółowy sposób:</span><span class="sxs-lookup"><span data-stu-id="cdcac-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cdcac-198">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cdcac-199">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="cdcac-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="cdcac-200">Składniki tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-200">Identity Components</span></span>

<span data-ttu-id="cdcac-201">Wszystkie pakiety NuGet zależne od tożsamości są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="cdcac-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="cdcac-202">Podstawowym pakietem tożsamości jest [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cdcac-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cdcac-203">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączony do programu `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="cdcac-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cdcac-204">Migrowanie do ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cdcac-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cdcac-205">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego magazynu tożsamości, zobacz [Migrowanie uwierzytelniania i tożsamości](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cdcac-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cdcac-206">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="cdcac-206">Setting password strength</span></span>

<span data-ttu-id="cdcac-207">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="cdcac-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cdcac-208">AddDefaultIdentity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cdcac-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cdcac-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cdcac-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cdcac-210">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="cdcac-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cdcac-211">Aby uzyskać więcej informacji, zobacz [Źródło AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cdcac-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="cdcac-212">Zapobiegaj publikowaniu zasobów tożsamości statycznej</span><span class="sxs-lookup"><span data-stu-id="cdcac-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cdcac-213">Aby zapobiec publikowaniu statycznych zasobów tożsamości (arkuszy stylów i plików JavaScript dla interfejsu użytkownika tożsamości) do katalogu głównego sieci Web, `ResolveStaticWebAssetsInputsDependsOn` Dodaj następującą `RemoveIdentityAssets` Właściwość i obiekt docelowy do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="cdcac-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="cdcac-214">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="cdcac-214">Next Steps</span></span>

* <span data-ttu-id="cdcac-215">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania tożsamości przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdcac-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="cdcac-216">Konfigurowanie systemu Identity</span><span class="sxs-lookup"><span data-stu-id="cdcac-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cdcac-217">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdcac-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cdcac-218">ASP.NET Core Identity to system członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="cdcac-219">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w tożsamości lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cdcac-220">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cdcac-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cdcac-221">Tożsamość można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cdcac-222">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cdcac-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cdcac-223">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cdcac-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cdcac-224">W tym temacie dowiesz się, jak używać tożsamości do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cdcac-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cdcac-225">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji korzystających z tożsamości, zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cdcac-226">AddDefaultIdentity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="cdcac-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cdcac-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cdcac-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cdcac-228">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="cdcac-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cdcac-229">Aby uzyskać więcej informacji, zobacz [Źródło AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cdcac-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cdcac-230">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="cdcac-230">Create a Web app with authentication</span></span>

<span data-ttu-id="cdcac-231">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cdcac-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdcac-233">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cdcac-234">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cdcac-235">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cdcac-236">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-236">Click **OK**.</span></span>
* <span data-ttu-id="cdcac-237">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cdcac-238">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="cdcac-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-239">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="cdcac-240">Wygenerowany projekt zapewnia [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cdcac-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cdcac-241">Biblioteka klas Razor tożsamość ujawnia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="cdcac-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cdcac-242">Przykład:</span><span class="sxs-lookup"><span data-stu-id="cdcac-242">For example:</span></span>

* <span data-ttu-id="cdcac-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cdcac-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="cdcac-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cdcac-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cdcac-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cdcac-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cdcac-246">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="cdcac-246">Apply migrations</span></span>

<span data-ttu-id="cdcac-247">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="cdcac-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdcac-249">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="cdcac-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-250">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cdcac-251">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="cdcac-251">Test Register and Login</span></span>

<span data-ttu-id="cdcac-252">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cdcac-252">Run the app and register a user.</span></span> <span data-ttu-id="cdcac-253">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cdcac-254">Konfigurowanie usług tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-254">Configure Identity services</span></span>

<span data-ttu-id="cdcac-255">Usługi są dodawane do `ConfigureServices`programu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cdcac-256">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="cdcac-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="cdcac-257">Poprzedni kod konfiguruje tożsamość z domyślnymi wartościami opcji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="cdcac-258">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cdcac-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cdcac-259">Tożsamość jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="cdcac-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cdcac-260">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="cdcac-261">Aby uzyskać więcej informacji, zobacz [IdentityOptions klasy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cdcac-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cdcac-262">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="cdcac-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="cdcac-263">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w projekcie Razor z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cdcac-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdcac-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdcac-265">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cdcac-266">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="cdcac-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cdcac-267">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="cdcac-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cdcac-268">W przeciwnym razie użyj prawidłowej przestrzeni nazw `ApplicationDbContext`dla:</span><span class="sxs-lookup"><span data-stu-id="cdcac-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cdcac-269">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="cdcac-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cdcac-270">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="cdcac-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cdcac-271">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="cdcac-271">Examine Register</span></span>

<span data-ttu-id="cdcac-272">Gdy użytkownik kliknie łącze **zarejestruj** , zostanie wywołana `RegisterModel.OnPostAsync` akcja.</span><span class="sxs-lookup"><span data-stu-id="cdcac-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cdcac-273">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) dla `_userManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="cdcac-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="cdcac-274">`_userManager`jest dostarczany przez wstrzyknięcie zależności):</span><span class="sxs-lookup"><span data-stu-id="cdcac-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="cdcac-275">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="cdcac-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cdcac-276">**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cdcac-277">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="cdcac-277">Log in</span></span>

<span data-ttu-id="cdcac-278">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="cdcac-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="cdcac-279">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cdcac-280">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="cdcac-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cdcac-281">Po przesłaniu formularza na stronie logowania zostanie wywołana `OnPostAsync` akcja.</span><span class="sxs-lookup"><span data-stu-id="cdcac-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cdcac-282">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu (dostarczone przez iniekcję zależności).</span><span class="sxs-lookup"><span data-stu-id="cdcac-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cdcac-283">Klasa bazowa `Controller` uwidacznia `User` właściwość, do której można uzyskać dostęp za pomocą metod kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cdcac-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="cdcac-284">Na przykład można wyliczyć `User.Claims` i podjąć decyzje dotyczące autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="cdcac-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="cdcac-285">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="cdcac-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cdcac-286">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="cdcac-286">Log out</span></span>

<span data-ttu-id="cdcac-287">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="cdcac-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="cdcac-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="cdcac-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cdcac-289">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="cdcac-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="cdcac-290">Testuj tożsamość</span><span class="sxs-lookup"><span data-stu-id="cdcac-290">Test Identity</span></span>

<span data-ttu-id="cdcac-291">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="cdcac-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cdcac-292">Aby przetestować tożsamość, Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.</span><span class="sxs-lookup"><span data-stu-id="cdcac-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cdcac-293">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="cdcac-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cdcac-294">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="cdcac-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cdcac-295">Eksploruj tożsamość</span><span class="sxs-lookup"><span data-stu-id="cdcac-295">Explore Identity</span></span>

<span data-ttu-id="cdcac-296">Aby poznać tożsamość w bardziej szczegółowy sposób:</span><span class="sxs-lookup"><span data-stu-id="cdcac-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cdcac-297">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cdcac-298">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="cdcac-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="cdcac-299">Składniki tożsamości</span><span class="sxs-lookup"><span data-stu-id="cdcac-299">Identity Components</span></span>

<span data-ttu-id="cdcac-300">Wszystkie pakiety NuGet zależne od tożsamości są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cdcac-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cdcac-301">Podstawowym pakietem tożsamości jest [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="cdcac-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cdcac-302">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączony do programu `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="cdcac-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cdcac-303">Migrowanie do ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cdcac-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cdcac-304">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego magazynu tożsamości, zobacz [Migrowanie uwierzytelniania i tożsamości](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cdcac-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cdcac-305">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="cdcac-305">Setting password strength</span></span>

<span data-ttu-id="cdcac-306">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="cdcac-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cdcac-307">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="cdcac-307">Next Steps</span></span>

* <span data-ttu-id="cdcac-308">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania tożsamości przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdcac-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="cdcac-309">Konfigurowanie systemu Identity</span><span class="sxs-lookup"><span data-stu-id="cdcac-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
