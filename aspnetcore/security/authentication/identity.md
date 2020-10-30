---
title: 'Wprowadzenie do :::no-loc(Identity)::: ASP.NET Core'
author: rick-anderson
description: 'Używanie :::no-loc(Identity)::: z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).'
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052022"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="da36a-104">Wprowadzenie do :::no-loc(Identity)::: ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da36a-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da36a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da36a-106">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="da36a-106">:::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="da36a-107">Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="da36a-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="da36a-108">Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.</span><span class="sxs-lookup"><span data-stu-id="da36a-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="da36a-109">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w :::no-loc(Identity)::: usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="da36a-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="da36a-110">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="da36a-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="da36a-111">[ :::no-loc(Identity)::: Kod źródłowy](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) jest dostępny w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="da36a-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="da36a-112">[Szkielet :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity) i Wyświetl wygenerowane pliki w celu przejrzenia interakcji z szablonem :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="da36a-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="da36a-113">:::no-loc(Identity)::: jest zazwyczaj konfigurowany przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="da36a-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="da36a-114">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="da36a-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="da36a-115">W tym temacie dowiesz się, jak używać :::no-loc(Identity)::: do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="da36a-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="da36a-116">Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="da36a-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="da36a-117">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają :::no-loc(Identity)::: , zobacz [następne kroki](#next).</span><span class="sxs-lookup"><span data-stu-id="da36a-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="da36a-118">[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:</span><span class="sxs-lookup"><span data-stu-id="da36a-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="da36a-119">Ewolucja platformy deweloperów Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="da36a-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="da36a-120">Niepowiązane z :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="da36a-120">Unrelated to :::no-loc(ASP.NET Core Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="da36a-121">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da36a-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="da36a-122">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="da36a-122">Create a Web app with authentication</span></span>

<span data-ttu-id="da36a-123">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="da36a-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da36a-125">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="da36a-125">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="da36a-126">Wybierz **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="da36a-126">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="da36a-127">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="da36a-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="da36a-128">Kliknij pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="da36a-128">Click **OK** .</span></span>
* <span data-ttu-id="da36a-129">Wybierz **aplikację sieci Web** ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="da36a-129">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="da36a-130">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="da36a-130">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-131">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="da36a-132">Poprzednie polecenie tworzy :::no-loc(Razor)::: aplikację internetową przy użyciu oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="da36a-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="da36a-133">Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="da36a-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="da36a-134">Wygenerowany projekt jest udostępniany [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) jako [ :::no-loc(Razor)::: Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="da36a-134">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="da36a-135">:::no-loc(Identity)::: :::no-loc(Razor)::: Biblioteka klas uwidacznia punkty końcowe z `:::no-loc(Identity):::` obszarem.</span><span class="sxs-lookup"><span data-stu-id="da36a-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="da36a-136">Przykład:</span><span class="sxs-lookup"><span data-stu-id="da36a-136">For example:</span></span>

* <span data-ttu-id="da36a-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="da36a-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="da36a-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="da36a-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="da36a-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="da36a-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="da36a-140">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="da36a-140">Apply migrations</span></span>

<span data-ttu-id="da36a-141">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="da36a-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da36a-143">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="da36a-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-144">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da36a-145">W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.</span><span class="sxs-lookup"><span data-stu-id="da36a-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="da36a-146">W przypadku LocalDB Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="da36a-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="da36a-147">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="da36a-147">Test Register and Login</span></span>

<span data-ttu-id="da36a-148">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="da36a-148">Run the app and register a user.</span></span> <span data-ttu-id="da36a-149">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="da36a-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="da36a-150">Konfigurowanie :::no-loc(Identity)::: usług</span><span class="sxs-lookup"><span data-stu-id="da36a-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="da36a-151">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="da36a-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="da36a-152">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="da36a-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="da36a-153">Poprzedni wyróżniony kod konfiguruje :::no-loc(Identity)::: domyślne wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="da36a-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="da36a-154">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da36a-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="da36a-155">:::no-loc(Identity)::: jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="da36a-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="da36a-156">`UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="da36a-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="da36a-157">Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="da36a-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="da36a-158">`app.UseAuthorization` jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację.</span><span class="sxs-lookup"><span data-stu-id="da36a-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="da36a-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="da36a-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="da36a-160">Aby uzyskać więcej informacji na temat programów `:::no-loc(Identity):::Options` i `Startup` , zobacz <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> i [Uruchamianie aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="da36a-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="da36a-161">Rejestrowanie szkieletów, logowanie, wylogowywanie i RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="da36a-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da36a-163">Dodaj `Register` pliki, `Login` , `LogOut` i `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="da36a-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="da36a-164">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w :::no-loc(Razor)::: projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="da36a-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-165">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da36a-166">Jeśli utworzono projekt o nazwie **WebApp1** , uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="da36a-166">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="da36a-167">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="da36a-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="da36a-168">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="da36a-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="da36a-169">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="da36a-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="da36a-170">Aby uzyskać więcej informacji na temat tworzenia szkieletów :::no-loc(Identity)::: , zobacz [tożsamość szkieletu w :::no-loc(Razor)::: projekcie z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="da36a-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="da36a-171">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="da36a-171">Examine Register</span></span>

<span data-ttu-id="da36a-172">Gdy użytkownik kliknie przycisk **zarejestruj** na `Register` stronie, `RegisterModel.OnPostAsync` Akcja jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="da36a-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="da36a-173">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="da36a-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="da36a-174">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="da36a-174">Log in</span></span>

<span data-ttu-id="da36a-175">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="da36a-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="da36a-176">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="da36a-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="da36a-177">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="da36a-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="da36a-178">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="da36a-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="da36a-179">`PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="da36a-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="da36a-180">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="da36a-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="da36a-181">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="da36a-181">Log out</span></span>

<span data-ttu-id="da36a-182">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="da36a-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="da36a-183">W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.</span><span class="sxs-lookup"><span data-stu-id="da36a-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="da36a-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="da36a-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="da36a-185">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="da36a-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="da36a-186">Badan :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-187">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="da36a-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="da36a-188">Aby przetestować :::no-loc(Identity)::: , Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="da36a-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="da36a-189">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="da36a-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="da36a-190">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="da36a-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="da36a-191">Przeglądać :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-192">Aby poznać :::no-loc(Identity)::: więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="da36a-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="da36a-193">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="da36a-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="da36a-194">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="da36a-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="da36a-195">:::no-loc(Identity)::: Komponentów</span><span class="sxs-lookup"><span data-stu-id="da36a-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="da36a-196">Wszystkie :::no-loc(Identity)::: zależne pakiety NuGet są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="da36a-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="da36a-197">Podstawowym pakietem :::no-loc(Identity)::: programu jest [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="da36a-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="da36a-198">Ten pakiet zawiera podstawowy zestaw interfejsów dla programu :::no-loc(ASP.NET Core Identity)::: i jest dołączany przez program `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="da36a-198">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="da36a-199">Migrowanie do programu :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-199">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="da36a-200">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego :::no-loc(Identity)::: magazynu, zobacz [Migrowanie :::no-loc(Identity)::: uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="da36a-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="da36a-201">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="da36a-201">Setting password strength</span></span>

<span data-ttu-id="da36a-202">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="da36a-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="da36a-203">ADDDEFAULT :::no-loc(Identity)::: i Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="da36a-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="da36a-205">Wywołanie `AddDefault:::no-loc(Identity):::` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="da36a-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="da36a-206">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="da36a-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="da36a-207">Zapobiegaj publikowaniu :::no-loc(Identity)::: zasobów statycznych</span><span class="sxs-lookup"><span data-stu-id="da36a-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="da36a-208">Aby uniemożliwić publikowanie statycznych :::no-loc(Identity)::: zasobów (arkuszy stylów i plików JavaScript dla :::no-loc(Identity)::: interfejsu użytkownika) w katalogu głównym sieci Web, Dodaj następującą `ResolveStaticWebAssetsInputsDependsOn` Właściwość i `Remove:::no-loc(Identity):::Assets` obiekt docelowy do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="da36a-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="da36a-209">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="da36a-209">Next Steps</span></span>

* <span data-ttu-id="da36a-210">[:::no-loc(ASP.NET Core Identity)::: kod źródłowy](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="da36a-210">[:::no-loc(ASP.NET Core Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="da36a-211">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania :::no-loc(Identity)::: za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="da36a-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="da36a-212">Ponowne :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da36a-213">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da36a-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da36a-214">:::no-loc(ASP.NET Core Identity)::: jest systemem członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="da36a-214">:::no-loc(ASP.NET Core Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="da36a-215">Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w :::no-loc(Identity)::: usłudze lub korzystać z zewnętrznego dostawcy logowania.</span><span class="sxs-lookup"><span data-stu-id="da36a-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="da36a-216">Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="da36a-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="da36a-217">:::no-loc(Identity)::: można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu.</span><span class="sxs-lookup"><span data-stu-id="da36a-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="da36a-218">Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="da36a-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="da36a-219">[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="da36a-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="da36a-220">W tym temacie dowiesz się, jak używać :::no-loc(Identity)::: do rejestrowania, logowania i wylogowywania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="da36a-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="da36a-221">Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają :::no-loc(Identity)::: , zobacz sekcję następne kroki na końcu tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="da36a-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="da36a-222">ADDDEFAULT :::no-loc(Identity)::: i Add:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> wprowadzono w ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="da36a-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="da36a-224">Wywołanie `AddDefault:::no-loc(Identity):::` jest podobne do wywołania następujących:</span><span class="sxs-lookup"><span data-stu-id="da36a-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="da36a-225">Aby uzyskać więcej informacji, zobacz [ADDDEFAULT :::no-loc(Identity)::: Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="da36a-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="da36a-226">Tworzenie aplikacji sieci Web z uwierzytelnianiem</span><span class="sxs-lookup"><span data-stu-id="da36a-226">Create a Web app with authentication</span></span>

<span data-ttu-id="da36a-227">Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="da36a-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="da36a-229">Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="da36a-229">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="da36a-230">Wybierz **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="da36a-230">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="da36a-231">Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu.</span><span class="sxs-lookup"><span data-stu-id="da36a-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="da36a-232">Kliknij pozycję **OK** .</span><span class="sxs-lookup"><span data-stu-id="da36a-232">Click **OK** .</span></span>
* <span data-ttu-id="da36a-233">Wybierz **aplikację sieci Web** ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="da36a-233">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="da36a-234">Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="da36a-234">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-235">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="da36a-236">Wygenerowany projekt jest udostępniany [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) jako [ :::no-loc(Razor)::: Biblioteka klas](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="da36a-236">The generated project provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="da36a-237">:::no-loc(Identity)::: :::no-loc(Razor)::: Biblioteka klas uwidacznia punkty końcowe z `:::no-loc(Identity):::` obszarem.</span><span class="sxs-lookup"><span data-stu-id="da36a-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="da36a-238">Przykład:</span><span class="sxs-lookup"><span data-stu-id="da36a-238">For example:</span></span>

* <span data-ttu-id="da36a-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="da36a-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="da36a-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="da36a-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="da36a-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="da36a-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="da36a-242">Zastosuj migracje</span><span class="sxs-lookup"><span data-stu-id="da36a-242">Apply migrations</span></span>

<span data-ttu-id="da36a-243">Zastosuj migracje, aby zainicjować bazę danych.</span><span class="sxs-lookup"><span data-stu-id="da36a-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da36a-245">Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):</span><span class="sxs-lookup"><span data-stu-id="da36a-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-246">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="da36a-247">Testuj rejestr i logowanie</span><span class="sxs-lookup"><span data-stu-id="da36a-247">Test Register and Login</span></span>

<span data-ttu-id="da36a-248">Uruchom aplikację i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="da36a-248">Run the app and register a user.</span></span> <span data-ttu-id="da36a-249">W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .</span><span class="sxs-lookup"><span data-stu-id="da36a-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="da36a-250">Konfigurowanie :::no-loc(Identity)::: usług</span><span class="sxs-lookup"><span data-stu-id="da36a-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="da36a-251">Usługi są dodawane do programu `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="da36a-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="da36a-252">Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.</span><span class="sxs-lookup"><span data-stu-id="da36a-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="da36a-253">Poprzedni kod konfiguruje :::no-loc(Identity)::: z użyciem domyślnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="da36a-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="da36a-254">Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="da36a-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="da36a-255">:::no-loc(Identity)::: jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="da36a-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="da36a-256">`UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="da36a-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="da36a-257">Aby uzyskać więcej informacji, zobacz [ :::no-loc(Identity)::: klasy opcji](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="da36a-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="da36a-258">Rejestrowanie, logowanie i wylogowywanie szkieletu</span><span class="sxs-lookup"><span data-stu-id="da36a-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="da36a-259">Postępuj zgodnie z informacjami o [tożsamości szkieletowej w :::no-loc(Razor)::: projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.</span><span class="sxs-lookup"><span data-stu-id="da36a-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="da36a-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da36a-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="da36a-261">Dodaj pliki rejestru, logowania i wylogowywania.</span><span class="sxs-lookup"><span data-stu-id="da36a-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="da36a-262">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="da36a-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="da36a-263">Jeśli utworzono projekt o nazwie **WebApp1** , uruchom następujące polecenia.</span><span class="sxs-lookup"><span data-stu-id="da36a-263">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="da36a-264">W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="da36a-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="da36a-265">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="da36a-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="da36a-266">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="da36a-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="da36a-267">Badaj rejestr</span><span class="sxs-lookup"><span data-stu-id="da36a-267">Examine Register</span></span>

<span data-ttu-id="da36a-268">Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="da36a-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="da36a-269">Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:</span><span class="sxs-lookup"><span data-stu-id="da36a-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="da36a-270">Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="da36a-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="da36a-271">**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.</span><span class="sxs-lookup"><span data-stu-id="da36a-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="da36a-272">Logowanie się</span><span class="sxs-lookup"><span data-stu-id="da36a-272">Log in</span></span>

<span data-ttu-id="da36a-273">Formularz logowania jest wyświetlany, gdy:</span><span class="sxs-lookup"><span data-stu-id="da36a-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="da36a-274">Wybrano łącze **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="da36a-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="da36a-275">Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.</span><span class="sxs-lookup"><span data-stu-id="da36a-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="da36a-276">Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja.</span><span class="sxs-lookup"><span data-stu-id="da36a-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="da36a-277">`PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.</span><span class="sxs-lookup"><span data-stu-id="da36a-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="da36a-278">Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="da36a-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="da36a-279">Wyloguj</span><span class="sxs-lookup"><span data-stu-id="da36a-279">Log out</span></span>

<span data-ttu-id="da36a-280">Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję.</span><span class="sxs-lookup"><span data-stu-id="da36a-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="da36a-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="da36a-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a :::no-loc(cookie):::.</span></span>

<span data-ttu-id="da36a-282">Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="da36a-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="da36a-283">Badan :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-284">Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych.</span><span class="sxs-lookup"><span data-stu-id="da36a-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="da36a-285">Aby przetestować :::no-loc(Identity)::: , Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.</span><span class="sxs-lookup"><span data-stu-id="da36a-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="da36a-286">Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="da36a-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="da36a-287">Nastąpi przekierowanie do strony logowania.</span><span class="sxs-lookup"><span data-stu-id="da36a-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="da36a-288">Przeglądać :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="da36a-289">Aby poznać :::no-loc(Identity)::: więcej szczegółów:</span><span class="sxs-lookup"><span data-stu-id="da36a-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="da36a-290">Utwórz źródło interfejsu użytkownika pełnej tożsamości</span><span class="sxs-lookup"><span data-stu-id="da36a-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="da36a-291">Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.</span><span class="sxs-lookup"><span data-stu-id="da36a-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="da36a-292">:::no-loc(Identity)::: Komponentów</span><span class="sxs-lookup"><span data-stu-id="da36a-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="da36a-293">Wszystkie :::no-loc(Identity)::: zależne pakiety NuGet są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="da36a-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="da36a-294">Podstawowym pakietem :::no-loc(Identity)::: programu jest [Microsoft. AspNetCore. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span><span class="sxs-lookup"><span data-stu-id="da36a-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="da36a-295">Ten pakiet zawiera podstawowy zestaw interfejsów dla programu :::no-loc(ASP.NET Core Identity)::: i jest dołączany przez program `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="da36a-295">This package contains the core set of interfaces for :::no-loc(ASP.NET Core Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="da36a-296">Migrowanie do programu :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-296">Migrating to :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="da36a-297">Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego :::no-loc(Identity)::: magazynu, zobacz [Migrowanie :::no-loc(Identity)::: uwierzytelniania i ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="da36a-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="da36a-298">Ustawianie siły hasła</span><span class="sxs-lookup"><span data-stu-id="da36a-298">Setting password strength</span></span>

<span data-ttu-id="da36a-299">Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.</span><span class="sxs-lookup"><span data-stu-id="da36a-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="da36a-300">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="da36a-300">Next Steps</span></span>

* <span data-ttu-id="da36a-301">Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania :::no-loc(Identity)::: za pomocą oprogramowania SQLite.</span><span class="sxs-lookup"><span data-stu-id="da36a-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="da36a-302">Ponowne :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="da36a-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
