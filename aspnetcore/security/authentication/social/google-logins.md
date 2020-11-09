---
title: Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono integrację uwierzytelniania użytkownika konta Google z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/google-logins
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060316"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="262d1-103">Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="262d1-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="262d1-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="262d1-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="262d1-105">W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się za pomocą konta Google przy użyciu projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="262d1-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="262d1-106">Tworzenie projektu konsoli interfejsu API firmy Google i identyfikatora klienta</span><span class="sxs-lookup"><span data-stu-id="262d1-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="262d1-107">Zainstaluj [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="262d1-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="262d1-108">Przejdź do [integracji usługi Google Sign-In w aplikacji sieci Web](https://developers.google.com/identity/sign-in/web/sign-in) i wybierz pozycję **Konfiguruj projekt** .</span><span class="sxs-lookup"><span data-stu-id="262d1-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) and select **Configure a project** .</span></span>
* <span data-ttu-id="262d1-109">W oknie dialogowym **Konfigurowanie klienta uwierzytelniania OAuth** wybierz opcję **serwer sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="262d1-109">In the **Configure your OAuth client** dialog, select **Web server** .</span></span>
* <span data-ttu-id="262d1-110">W polu tekstowym **autoryzowane adresy URI przekierowania** ustaw identyfikator URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="262d1-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="262d1-111">Na przykład `https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="262d1-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="262d1-112">Zapisz **Identyfikator klienta** i **klucz tajny klienta** .</span><span class="sxs-lookup"><span data-stu-id="262d1-112">Save the **Client ID** and **Client Secret** .</span></span>
* <span data-ttu-id="262d1-113">Podczas wdrażania lokacji Zarejestruj nowy publiczny adres URL z poziomu **konsoli Google** .</span><span class="sxs-lookup"><span data-stu-id="262d1-113">When deploying the site, register the new public url from the **Google Console** .</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="262d1-114">Zapisz identyfikator i klucz tajny klienta Google</span><span class="sxs-lookup"><span data-stu-id="262d1-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="262d1-115">Przechowuj ustawienia poufne, takie jak identyfikator klienta Google i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="262d1-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="262d1-116">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="262d1-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="262d1-117">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="262d1-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="262d1-118">Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Google:ClientId` i `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="262d1-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="262d1-119">Poświadczenia interfejsu API i użycie można zarządzać w [konsoli interfejsu API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="262d1-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="262d1-120">Skonfiguruj uwierzytelnianie Google</span><span class="sxs-lookup"><span data-stu-id="262d1-120">Configure Google authentication</span></span>

<span data-ttu-id="262d1-121">Dodaj usługę Google do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="262d1-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="262d1-122">Zaloguj się przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="262d1-122">Sign in with Google</span></span>

* <span data-ttu-id="262d1-123">Uruchom aplikację i kliknij pozycję **Zaloguj** .</span><span class="sxs-lookup"><span data-stu-id="262d1-123">Run the app and click **Log in** .</span></span> <span data-ttu-id="262d1-124">Zostanie wyświetlona opcja zalogowania się za pomocą usługi Google.</span><span class="sxs-lookup"><span data-stu-id="262d1-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="262d1-125">Kliknij przycisk **Google** , który przekierowuje do usługi Google w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="262d1-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="262d1-126">Po wprowadzeniu poświadczeń Google nastąpi przekierowanie z powrotem do witryny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="262d1-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="262d1-127"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Więcej informacji o opcjach konfiguracji obsługiwanych przez uwierzytelnianie Google można znaleźć w dokumentacji interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="262d1-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="262d1-128">Może to służyć do żądania różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="262d1-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="262d1-129">Zmień domyślny identyfikator URI wywołania zwrotnego</span><span class="sxs-lookup"><span data-stu-id="262d1-129">Change the default callback URI</span></span>

<span data-ttu-id="262d1-130">Segment identyfikatora URI `/signin-google` jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Google.</span><span class="sxs-lookup"><span data-stu-id="262d1-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="262d1-131">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego usługi Google Authentication za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="262d1-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="262d1-132">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="262d1-132">Troubleshooting</span></span>

* <span data-ttu-id="262d1-133">Jeśli logowanie nie działa i nie pojawiają się żadne błędy, przełącz się do trybu deweloperskiego, aby ułatwić debugowanie problemu.</span><span class="sxs-lookup"><span data-stu-id="262d1-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="262d1-134">Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia wyników w *argumencieexception: należy podać opcję "SignInScheme"* .</span><span class="sxs-lookup"><span data-stu-id="262d1-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="262d1-135">Szablon projektu używany w tym samouczku zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="262d1-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="262d1-136">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, podczas *przetwarzania błędu żądania nie można wykonać operacji bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="262d1-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="262d1-137">Wybierz pozycję **Zastosuj migracje** , aby utworzyć bazę danych, a następnie Odśwież stronę, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="262d1-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="262d1-138">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="262d1-138">Next steps</span></span>

* <span data-ttu-id="262d1-139">W tym artykule pokazano, jak można uwierzytelniać za pomocą usługi Google.</span><span class="sxs-lookup"><span data-stu-id="262d1-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="262d1-140">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="262d1-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="262d1-141">Po opublikowaniu aplikacji na platformie Azure zresetuj ją `ClientSecret` w konsoli interfejsu API firmy Google.</span><span class="sxs-lookup"><span data-stu-id="262d1-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="262d1-142">Ustaw `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Ustawienia aplikacji i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="262d1-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="262d1-143">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="262d1-143">The configuration system is set up to read keys from environment variables.</span></span>
