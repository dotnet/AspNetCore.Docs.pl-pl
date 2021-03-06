---
title: Wprowadzenie do Identity ASP.NET Core
author: rick-anderson
description: Używanie Identity z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 4fa49f795b78b88e00bd32d04f74acd8689383b2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394476"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="8ea8b-104">Wprowadzenie do Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8ea8b-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ea8b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ea8b-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="8ea8b-107">Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="8ea8b-108">Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="8ea8b-109">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="8ea8b-110">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="8ea8b-111">[ Identity Kod źródłowy](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) jest dostępny w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="8ea8b-112">[Szkielet Identity ](xref:security/authentication/scaffold-identity) i Wyświetl wygenerowane pliki w celu przejrzenia interakcji z szablonem Identity .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="8ea8b-113">Identity jest zazwyczaj konfigurowany przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="8ea8b-114">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="8ea8b-115">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="8ea8b-116">Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="8ea8b-117">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz [następne kroki](#next).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="8ea8b-118">[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="8ea8b-119">Ewolucja platformy deweloperów Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="8ea8b-120">Niepowiązane z ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="8ea8b-121">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="8ea8b-122">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="8ea8b-122">Create a Web app with authentication</span></span>

<span data-ttu-id="8ea8b-123">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ea8b-125">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="8ea8b-126">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="8ea8b-127">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="8ea8b-128">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-128">Click **OK**.</span></span>
* <span data-ttu-id="8ea8b-129">Wybierz **aplikację sieci Web** ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="8ea8b-130">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-131">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="8ea8b-132">Poprzednie polecenie tworzy Razor aplikację internetową przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="8ea8b-133">Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="8ea8b-134">Wygenerowany projekt jest udostępniany [ASP.NET Core Identity](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="8ea8b-135">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="8ea8b-136">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-136">For example:</span></span>

* <span data-ttu-id="8ea8b-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="8ea8b-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="8ea8b-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="8ea8b-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="8ea8b-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="8ea8b-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="8ea8b-140">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="8ea8b-140">Apply migrations</span></span>

<span data-ttu-id="8ea8b-141">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea8b-143">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="8ea8b-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-144">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8ea8b-145">W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8ea8b-146">W przypadku LocalDB Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="8ea8b-147">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="8ea8b-147">Test Register and Login</span></span>

<span data-ttu-id="8ea8b-148">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-148">Run the app and register a user.</span></span> <span data-ttu-id="8ea8b-149">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="8ea8b-150">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="8ea8b-150">Configure Identity services</span></span>

<span data-ttu-id="8ea8b-151">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="8ea8b-152">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="8ea8b-153">Poprzedni wyróżniony kod konfiguruje Identity domyślne wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="8ea8b-154">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8ea8b-155">Identity jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="8ea8b-156">`UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="8ea8b-157">Poprzedni kod konfiguruje Identity z użyciem domyślnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="8ea8b-158">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8ea8b-159">Identity jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="8ea8b-160">`UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8ea8b-161">Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="8ea8b-162">`app.UseAuthorization` jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="8ea8b-163">`UseRouting`, `UseAuthentication` , `UseAuthorization` i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="8ea8b-164">Aby uzyskać więcej informacji na temat programów `IdentityOptions` i `Startup` , zobacz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="8ea8b-165">Rejestrowanie szkieletów, logowanie, wylogowywanie i RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="8ea8b-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea8b-167">Dodaj `Register` pliki, `Login` , `LogOut` i `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="8ea8b-168">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-169">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8ea8b-170">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="8ea8b-171">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="8ea8b-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="8ea8b-172">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="8ea8b-173">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="8ea8b-174">Aby uzyskać więcej informacji na temat tworzenia szkieletów Identity , zobacz [tożsamość szkieletu w Razor projekcie z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="8ea8b-175">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="8ea8b-175">Examine Register</span></span>

<span data-ttu-id="8ea8b-176">Gdy użytkownik kliknie przycisk **zarejestruj** na `Register` stronie, `RegisterModel.OnPostAsync` Akcja jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="8ea8b-177">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="8ea8b-178">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="8ea8b-178">Log in</span></span>

<span data-ttu-id="8ea8b-179">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="8ea8b-180">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="8ea8b-181">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="8ea8b-182">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="8ea8b-183">`PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="8ea8b-184">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="8ea8b-185">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="8ea8b-185">Log out</span></span>

<span data-ttu-id="8ea8b-186">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="8ea8b-187">W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="8ea8b-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="8ea8b-189">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="8ea8b-190">Badan Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-190">Test Identity</span></span>

<span data-ttu-id="8ea8b-191">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="8ea8b-192">Aby przetestować Identity , Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="8ea8b-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="8ea8b-193">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="8ea8b-194">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="8ea8b-195">Przeglądać Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-195">Explore Identity</span></span>

<span data-ttu-id="8ea8b-196">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="8ea8b-197">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="8ea8b-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="8ea8b-198">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="8ea8b-199">Identity Komponentów</span><span class="sxs-lookup"><span data-stu-id="8ea8b-199">Identity Components</span></span>

<span data-ttu-id="8ea8b-200">Wszystkie Identity zależne pakiety NuGet są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="8ea8b-201">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="8ea8b-202">Ten pakiet zawiera podstawowy zestaw interfejsów dla programu ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="8ea8b-203">Migrowanie do programu ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="8ea8b-204">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="8ea8b-205">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="8ea8b-205">Setting password strength</span></span>

<span data-ttu-id="8ea8b-206">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="8ea8b-207">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="8ea8b-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="8ea8b-209">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="8ea8b-210">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="8ea8b-211">Zapobiegaj publikowaniu Identity zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="8ea8b-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="8ea8b-212">Aby uniemożliwić publikowanie statycznych Identity zasobów (arkuszy stylów i plików JavaScript dla Identity interfejsu użytkownika) w katalogu głównym sieci Web, Dodaj następującą `ResolveStaticWebAssetsInputsDependsOn` Właściwość i `RemoveIdentityAssets` obiekt docelowy do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="8ea8b-213">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="8ea8b-213">Next Steps</span></span>

* <span data-ttu-id="8ea8b-214">[ASP.NET Core Identity kod źródłowy](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="8ea8b-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="8ea8b-215">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="8ea8b-216">Ponowne Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8ea8b-217">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ea8b-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ea8b-218">ASP.NET Core Identity jest systemem członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="8ea8b-219">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="8ea8b-220">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="8ea8b-221">Identity można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="8ea8b-222">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="8ea8b-223">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8ea8b-224">W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="8ea8b-225">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="8ea8b-226">ADDDEFAULT Identity i AddIdentity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="8ea8b-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="8ea8b-228">Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="8ea8b-229">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="8ea8b-230">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="8ea8b-230">Create a Web app with authentication</span></span>

<span data-ttu-id="8ea8b-231">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ea8b-233">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="8ea8b-234">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="8ea8b-235">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="8ea8b-236">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-236">Click **OK**.</span></span>
* <span data-ttu-id="8ea8b-237">Wybierz **aplikację sieci Web** ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="8ea8b-238">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-239">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="8ea8b-240">Wygenerowany projekt jest udostępniany [ASP.NET Core Identity](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="8ea8b-241">Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="8ea8b-242">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-242">For example:</span></span>

* <span data-ttu-id="8ea8b-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="8ea8b-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="8ea8b-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="8ea8b-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="8ea8b-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="8ea8b-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="8ea8b-246">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="8ea8b-246">Apply migrations</span></span>

<span data-ttu-id="8ea8b-247">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea8b-249">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="8ea8b-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-250">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="8ea8b-251">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="8ea8b-251">Test Register and Login</span></span>

<span data-ttu-id="8ea8b-252">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-252">Run the app and register a user.</span></span> <span data-ttu-id="8ea8b-253">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="8ea8b-254">Konfigurowanie Identity usług</span><span class="sxs-lookup"><span data-stu-id="8ea8b-254">Configure Identity services</span></span>

<span data-ttu-id="8ea8b-255">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="8ea8b-256">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="8ea8b-257">Poprzedni kod konfiguruje Identity z użyciem domyślnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="8ea8b-258">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8ea8b-259">Identity jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="8ea8b-260">`UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="8ea8b-261">Aby uzyskać więcej informacji, zobacz [ Identity klasy opcji](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="8ea8b-262">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="8ea8b-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="8ea8b-263">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8ea8b-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea8b-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea8b-265">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8ea8b-266">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="8ea8b-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8ea8b-267">Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="8ea8b-268">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="8ea8b-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

<span data-ttu-id="8ea8b-269">W przypadku korzystania z oprogramowania SQLite `--useSqLite` należy określić:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-269">When using SQLite, `--useSqLite` must be specified:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

<span data-ttu-id="8ea8b-270">W programie SQL Express Użyj następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-270">With SQL Express, use the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="8ea8b-271">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-271">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="8ea8b-272">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-272">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="8ea8b-273">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="8ea8b-273">Examine Register</span></span>

<span data-ttu-id="8ea8b-274">Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-274">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="8ea8b-275">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-275">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="8ea8b-276">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-276">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="8ea8b-277">**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-277">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="8ea8b-278">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="8ea8b-278">Log in</span></span>

<span data-ttu-id="8ea8b-279">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-279">The Login form is displayed when:</span></span>

* <span data-ttu-id="8ea8b-280">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-280">The **Log in** link is selected.</span></span>
* <span data-ttu-id="8ea8b-281">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-281">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="8ea8b-282">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-282">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="8ea8b-283">`PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-283">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="8ea8b-284">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-284">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="8ea8b-285">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="8ea8b-285">Log out</span></span>

<span data-ttu-id="8ea8b-286">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="8ea8b-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w cookie .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="8ea8b-288">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="8ea8b-289">Badan Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-289">Test Identity</span></span>

<span data-ttu-id="8ea8b-290">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="8ea8b-291">Aby przetestować Identity , Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="8ea8b-292">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="8ea8b-293">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="8ea8b-294">Przeglądać Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-294">Explore Identity</span></span>

<span data-ttu-id="8ea8b-295">Aby poznać Identity więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="8ea8b-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="8ea8b-296">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="8ea8b-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="8ea8b-297">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="8ea8b-298">Identity Komponentów</span><span class="sxs-lookup"><span data-stu-id="8ea8b-298">Identity Components</span></span>

<span data-ttu-id="8ea8b-299">Wszystkie Identity zależne pakiety NuGet są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8ea8b-300">Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="8ea8b-301">Ten pakiet zawiera podstawowy zestaw interfejsów dla programu ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="8ea8b-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="8ea8b-302">Migrowanie do programu ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="8ea8b-303">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="8ea8b-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="8ea8b-304">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="8ea8b-304">Setting password strength</span></span>

<span data-ttu-id="8ea8b-305">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8ea8b-306">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="8ea8b-306">Next Steps</span></span>

* <span data-ttu-id="8ea8b-307">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="8ea8b-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="8ea8b-308">Ponowne Identity</span><span class="sxs-lookup"><span data-stu-id="8ea8b-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
