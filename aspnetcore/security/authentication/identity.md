---
title: Wprowadzenie do Identity ASP.NET Core
author: rick-anderson
description: Używanie Identity z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6ac565bfa4862168fa143417ab5a81c51b620f16
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212452"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="83a93-104">Wprowadzenie do Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83a93-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83a93-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83a93-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="83a93-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="83a93-107">Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a93-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="83a93-108">Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.</span><span class="sxs-lookup"><span data-stu-id="83a93-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="83a93-109">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="83a93-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="83a93-110">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="83a93-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="83a93-111">[ Identity Kod źródłowy](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) jest dostępny w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="83a93-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="83a93-112">[Szkielet Identity ](xref:security/authentication/scaffold-identity) i Wyświetl wygenerowane pliki w celu przejrzenia interakcji z szablonem Identity .</span><span class="sxs-lookup"><span data-stu-id="83a93-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="83a93-113">jest zazwyczaj konfigurowany przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="83a93-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="83a93-114">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="83a93-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="83a93-115">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a93-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="83a93-116">Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83a93-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="83a93-117">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="83a93-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="83a93-118">[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:</span><span class="sxs-lookup"><span data-stu-id="83a93-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="83a93-119">Ewolucja platformy deweloperów Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="83a93-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="83a93-120">Niepowiązane z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="83a93-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="83a93-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="83a93-121">Server4.md)]</span></span>

<span data-ttu-id="83a93-122">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="83a93-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="83a93-123">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="83a93-123">Create a Web app with authentication</span></span>

<span data-ttu-id="83a93-124">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83a93-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-125">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83a93-126">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="83a93-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="83a93-127">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83a93-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="83a93-128">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="83a93-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="83a93-129">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="83a93-129">Click **OK**.</span></span>
* <span data-ttu-id="83a93-130">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="83a93-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="83a93-131">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="83a93-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-132">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="83a93-133">Poprzednie polecenie tworzy Razor aplikację internetową przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="83a93-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="83a93-134">Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="83a93-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="83a93-135">Wygenerowany projekt zawiera [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="83a93-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="83a93-136">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="83a93-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="83a93-137">Przykład:</span><span class="sxs-lookup"><span data-stu-id="83a93-137">For example:</span></span>

* <span data-ttu-id="83a93-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="83a93-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="83a93-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="83a93-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="83a93-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="83a93-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="83a93-141">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="83a93-141">Apply migrations</span></span>

<span data-ttu-id="83a93-142">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83a93-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-143">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83a93-144">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="83a93-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-145">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="83a93-146">W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="83a93-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="83a93-147">W przypadku LocalDB Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="83a93-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="83a93-148">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="83a93-148">Test Register and Login</span></span>

<span data-ttu-id="83a93-149">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a93-149">Run the app and register a user.</span></span> <span data-ttu-id="83a93-150">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="83a93-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="83a93-151">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="83a93-151">Configure Identity services</span></span>

<span data-ttu-id="83a93-152">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="83a93-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="83a93-153">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="83a93-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="83a93-154">Poprzedni wyróżniony kod konfiguruje Identity domyślne wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="83a93-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="83a93-155">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="83a93-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="83a93-156">jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="83a93-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="83a93-157">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="83a93-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="83a93-158">Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="83a93-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="83a93-159">`app.UseAuthorization`jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację.</span><span class="sxs-lookup"><span data-stu-id="83a93-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="83a93-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="83a93-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="83a93-161">Aby uzyskać więcej informacji na temat programów `IdentityOptions` i `Startup` , zobacz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="83a93-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="83a93-162">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="83a93-162">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-163">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83a93-164">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="83a93-164">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="83a93-165">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="83a93-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-166">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="83a93-167">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="83a93-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="83a93-168">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="83a93-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="83a93-169">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="83a93-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="83a93-170">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="83a93-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="83a93-171">Aby uzyskać więcej informacji na temat tworzenia szkieletów Identity , zobacz [tożsamość szkieletu w Razor projekcie z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="83a93-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="83a93-172">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="83a93-172">Examine Register</span></span>

<span data-ttu-id="83a93-173">Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="83a93-173">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="83a93-174">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="83a93-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="83a93-175">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="83a93-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="83a93-176">Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83a93-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="83a93-177">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="83a93-177">Log in</span></span>

<span data-ttu-id="83a93-178">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="83a93-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="83a93-179">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="83a93-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="83a93-180">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="83a93-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="83a93-181">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="83a93-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="83a93-182">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="83a93-182">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="83a93-183">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="83a93-183">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="83a93-184">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="83a93-184">Log out</span></span>

<span data-ttu-id="83a93-185">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="83a93-185">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="83a93-186">W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="83a93-186">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="83a93-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="83a93-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="83a93-188">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="83a93-188">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="83a93-189">BadanIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-189">Test Identity</span></span>

<span data-ttu-id="83a93-190">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="83a93-190">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="83a93-191">Aby przetestować Identity , Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="83a93-191">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="83a93-192">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="83a93-192">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="83a93-193">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="83a93-193">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="83a93-194">PrzeglądaćIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-194">Explore Identity</span></span>

<span data-ttu-id="83a93-195">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="83a93-195">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="83a93-196">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="83a93-196">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="83a93-197">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="83a93-197">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="83a93-198">Komponentów</span><span class="sxs-lookup"><span data-stu-id="83a93-198"> Components</span></span>

<span data-ttu-id="83a93-199">Wszystkie Identity zależne pakiety NuGet są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="83a93-199">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="83a93-200">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="83a93-200">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="83a93-201">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="83a93-201">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="83a93-202">Migrowanie do ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-202">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="83a93-203">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="83a93-203">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="83a93-204">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="83a93-204">Setting password strength</span></span>

<span data-ttu-id="83a93-205">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="83a93-205">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="83a93-206">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-206">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="83a93-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="83a93-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="83a93-208">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="83a93-208">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="83a93-209">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="83a93-209">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="83a93-210">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="83a93-210">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="83a93-211">Aby uniemożliwić publikowanie statycznych Identity zasobów (arkuszy stylów i plików JavaScript dla Identity interfejsu użytkownika) w katalogu głównym sieci Web, Dodaj następującą `ResolveStaticWebAssetsInputsDependsOn` Właściwość i `RemoveIdentityAssets` obiekt docelowy do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="83a93-211">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="83a93-212">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="83a93-212">Next Steps</span></span>

* <span data-ttu-id="83a93-213">[ASP.NET Core Identity kod źródłowy](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="83a93-213">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="83a93-214">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="83a93-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="83a93-215">[PonowneIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="83a93-215">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83a93-216">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83a93-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83a93-217">ASP.NET Core Identity to system członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83a93-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="83a93-218">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="83a93-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="83a93-219">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="83a93-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="83a93-220">można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="83a93-220"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="83a93-221">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="83a93-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="83a93-222">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="83a93-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="83a93-223">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a93-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="83a93-224">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="83a93-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="83a93-225">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="83a93-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="83a93-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="83a93-227">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="83a93-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="83a93-228">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="83a93-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="83a93-229">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="83a93-229">Create a Web app with authentication</span></span>

<span data-ttu-id="83a93-230">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83a93-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-231">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83a93-232">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="83a93-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="83a93-233">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="83a93-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="83a93-234">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="83a93-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="83a93-235">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="83a93-235">Click **OK**.</span></span>
* <span data-ttu-id="83a93-236">Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="83a93-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="83a93-237">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="83a93-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-238">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="83a93-239">Wygenerowany projekt zawiera [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor bibliotekę klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="83a93-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="83a93-240">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="83a93-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="83a93-241">Przykład:</span><span class="sxs-lookup"><span data-stu-id="83a93-241">For example:</span></span>

* <span data-ttu-id="83a93-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="83a93-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="83a93-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="83a93-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="83a93-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="83a93-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="83a93-245">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="83a93-245">Apply migrations</span></span>

<span data-ttu-id="83a93-246">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="83a93-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-247">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83a93-248">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="83a93-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-249">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="83a93-250">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="83a93-250">Test Register and Login</span></span>

<span data-ttu-id="83a93-251">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="83a93-251">Run the app and register a user.</span></span> <span data-ttu-id="83a93-252">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="83a93-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="83a93-253">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="83a93-253">Configure Identity services</span></span>

<span data-ttu-id="83a93-254">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="83a93-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="83a93-255">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="83a93-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="83a93-256">Poprzedni kod konfiguruje Identity z użyciem domyślnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="83a93-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="83a93-257">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="83a93-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="83a93-258">jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="83a93-258"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="83a93-259">`UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="83a93-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="83a93-260">Aby uzyskać więcej informacji, zobacz [ Identity klasy opcji](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="83a93-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="83a93-261">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="83a93-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="83a93-262">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="83a93-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83a93-263">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83a93-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83a93-264">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="83a93-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="83a93-265">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="83a93-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="83a93-266">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="83a93-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="83a93-267">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="83a93-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="83a93-268">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="83a93-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="83a93-269">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="83a93-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="83a93-270">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="83a93-270">Examine Register</span></span>

<span data-ttu-id="83a93-271">Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="83a93-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="83a93-272">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="83a93-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="83a93-273">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="83a93-273">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="83a93-274">**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="83a93-274">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="83a93-275">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="83a93-275">Log in</span></span>

<span data-ttu-id="83a93-276">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="83a93-276">The Login form is displayed when:</span></span>

* <span data-ttu-id="83a93-277">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="83a93-277">The **Log in** link is selected.</span></span>
* <span data-ttu-id="83a93-278">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="83a93-278">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="83a93-279">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="83a93-279">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="83a93-280">`PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="83a93-280">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="83a93-281">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="83a93-281">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="83a93-282">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="83a93-282">Log out</span></span>

<span data-ttu-id="83a93-283">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="83a93-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="83a93-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.</span><span class="sxs-lookup"><span data-stu-id="83a93-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="83a93-285">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="83a93-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="83a93-286">BadanIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-286">Test Identity</span></span>

<span data-ttu-id="83a93-287">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="83a93-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="83a93-288">Aby przetestować Identity , Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.</span><span class="sxs-lookup"><span data-stu-id="83a93-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="83a93-289">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="83a93-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="83a93-290">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="83a93-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="83a93-291">PrzeglądaćIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-291">Explore Identity</span></span>

<span data-ttu-id="83a93-292">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="83a93-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="83a93-293">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="83a93-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="83a93-294">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="83a93-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="83a93-295">Komponentów</span><span class="sxs-lookup"><span data-stu-id="83a93-295"> Components</span></span>

<span data-ttu-id="83a93-296">Wszystkie Identity zależne pakiety NuGet są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="83a93-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="83a93-297">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="83a93-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="83a93-298">Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="83a93-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="83a93-299">Migrowanie do ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="83a93-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="83a93-300">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="83a93-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="83a93-301">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="83a93-301">Setting password strength</span></span>

<span data-ttu-id="83a93-302">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="83a93-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="83a93-303">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="83a93-303">Next Steps</span></span>

* <span data-ttu-id="83a93-304">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="83a93-304">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="83a93-305">[PonowneIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="83a93-305">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
