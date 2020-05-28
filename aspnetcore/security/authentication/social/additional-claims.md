---
<span data-ttu-id="6fbd0-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-102">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-103">'Identity'</span></span>
- <span data-ttu-id="6fbd0-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-105">'Razor'</span></span>
- <span data-ttu-id="6fbd0-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="6fbd0-107">Utrwalaj dodatkowe oświadczenia i tokeny od zewnętrznych dostawców w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fbd0-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fbd0-108">Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6fbd0-109">Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6fbd0-110">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6fbd0-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6fbd0-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6fbd0-111">Prerequisites</span></span>

<span data-ttu-id="6fbd0-112">Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6fbd0-113">Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6fbd0-114">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6fbd0-115">Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="6fbd0-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6fbd0-116">Ustawianie identyfikatora klienta i klucza tajnego klienta</span><span class="sxs-lookup"><span data-stu-id="6fbd0-116">Set the client ID and client secret</span></span>

<span data-ttu-id="6fbd0-117">Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6fbd0-118">Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6fbd0-119">Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6fbd0-120">Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6fbd0-121">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="6fbd0-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6fbd0-122">Uwierzytelnianie przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="6fbd0-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6fbd0-123">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6fbd0-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6fbd0-124">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="6fbd0-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6fbd0-125">Inni dostawcy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6fbd0-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6fbd0-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6fbd0-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6fbd0-127">Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6fbd0-128">Ustanów zakres uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6fbd0-128">Establish the authentication scope</span></span>

<span data-ttu-id="6fbd0-129">Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6fbd0-130">Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6fbd0-131">Dostawca</span><span class="sxs-lookup"><span data-stu-id="6fbd0-131">Provider</span></span>  | <span data-ttu-id="6fbd0-132">Zakres</span><span class="sxs-lookup"><span data-stu-id="6fbd0-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="6fbd0-133">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-134">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-135">'Identity'</span></span>
- <span data-ttu-id="6fbd0-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-137">'Razor'</span></span>
- <span data-ttu-id="6fbd0-138">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-139">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-140">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-141">'Identity'</span></span>
- <span data-ttu-id="6fbd0-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-143">'Razor'</span></span>
- <span data-ttu-id="6fbd0-144">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-144">'SignalR' uid:</span></span> 

<span data-ttu-id="6fbd0-145">----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-146">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-147">'Identity'</span></span>
- <span data-ttu-id="6fbd0-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-149">'Razor'</span></span>
- <span data-ttu-id="6fbd0-150">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-151">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-152">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-153">'Identity'</span></span>
- <span data-ttu-id="6fbd0-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-155">'Razor'</span></span>
- <span data-ttu-id="6fbd0-156">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-157">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-158">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-159">'Identity'</span></span>
- <span data-ttu-id="6fbd0-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-161">'Razor'</span></span>
- <span data-ttu-id="6fbd0-162">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-163">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-164">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-165">'Identity'</span></span>
- <span data-ttu-id="6fbd0-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-167">'Razor'</span></span>
- <span data-ttu-id="6fbd0-168">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-169">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-170">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-171">'Identity'</span></span>
- <span data-ttu-id="6fbd0-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-173">'Razor'</span></span>
- <span data-ttu-id="6fbd0-174">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-175">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-176">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-177">'Identity'</span></span>
- <span data-ttu-id="6fbd0-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-179">'Razor'</span></span>
- <span data-ttu-id="6fbd0-180">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-181">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-182">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-183">'Identity'</span></span>
- <span data-ttu-id="6fbd0-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-185">'Razor'</span></span>
- <span data-ttu-id="6fbd0-186">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-187">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-188">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-189">'Identity'</span></span>
- <span data-ttu-id="6fbd0-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-191">'Razor'</span></span>
- <span data-ttu-id="6fbd0-192">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-193">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-194">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-195">'Identity'</span></span>
- <span data-ttu-id="6fbd0-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-197">'Razor'</span></span>
- <span data-ttu-id="6fbd0-198">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-199">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-200">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-201">'Identity'</span></span>
- <span data-ttu-id="6fbd0-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-203">'Razor'</span></span>
- <span data-ttu-id="6fbd0-204">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-205">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-206">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-207">'Identity'</span></span>
- <span data-ttu-id="6fbd0-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-209">'Razor'</span></span>
- <span data-ttu-id="6fbd0-210">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-211">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-212">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-213">'Identity'</span></span>
- <span data-ttu-id="6fbd0-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-215">'Razor'</span></span>
- <span data-ttu-id="6fbd0-216">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-217">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-218">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-219">'Identity'</span></span>
- <span data-ttu-id="6fbd0-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-221">'Razor'</span></span>
- <span data-ttu-id="6fbd0-222">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-223">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-224">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-225">'Identity'</span></span>
- <span data-ttu-id="6fbd0-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-227">'Razor'</span></span>
- <span data-ttu-id="6fbd0-228">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-229">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-230">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-231">'Identity'</span></span>
- <span data-ttu-id="6fbd0-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-233">'Razor'</span></span>
- <span data-ttu-id="6fbd0-234">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-235">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-236">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-237">'Identity'</span></span>
- <span data-ttu-id="6fbd0-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-239">'Razor'</span></span>
- <span data-ttu-id="6fbd0-240">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-241">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-242">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-243">'Identity'</span></span>
- <span data-ttu-id="6fbd0-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-245">'Razor'</span></span>
- <span data-ttu-id="6fbd0-246">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-247">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-248">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-249">'Identity'</span></span>
- <span data-ttu-id="6fbd0-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-251">'Razor'</span></span>
- <span data-ttu-id="6fbd0-252">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-253">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-254">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-255">'Identity'</span></span>
- <span data-ttu-id="6fbd0-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-257">'Razor'</span></span>
- <span data-ttu-id="6fbd0-258">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-259">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-260">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-261">'Identity'</span></span>
- <span data-ttu-id="6fbd0-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-263">'Razor'</span></span>
- <span data-ttu-id="6fbd0-264">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-265">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-266">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-267">'Identity'</span></span>
- <span data-ttu-id="6fbd0-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-269">'Razor'</span></span>
- <span data-ttu-id="6fbd0-270">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-271">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-272">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-273">'Identity'</span></span>
- <span data-ttu-id="6fbd0-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-275">'Razor'</span></span>
- <span data-ttu-id="6fbd0-276">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-277">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-278">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-279">'Identity'</span></span>
- <span data-ttu-id="6fbd0-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-281">'Razor'</span></span>
- <span data-ttu-id="6fbd0-282">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-283">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-284">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-285">'Identity'</span></span>
- <span data-ttu-id="6fbd0-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-287">'Razor'</span></span>
- <span data-ttu-id="6fbd0-288">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-289">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-290">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-291">'Identity'</span></span>
- <span data-ttu-id="6fbd0-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-293">'Razor'</span></span>
- <span data-ttu-id="6fbd0-294">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-295">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-296">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-297">'Identity'</span></span>
- <span data-ttu-id="6fbd0-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-299">'Razor'</span></span>
- <span data-ttu-id="6fbd0-300">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-301">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-302">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-303">'Identity'</span></span>
- <span data-ttu-id="6fbd0-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-305">'Razor'</span></span>
- <span data-ttu-id="6fbd0-306">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-307">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-308">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-309">'Identity'</span></span>
- <span data-ttu-id="6fbd0-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-311">'Razor'</span></span>
- <span data-ttu-id="6fbd0-312">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-313">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-314">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-315">'Identity'</span></span>
- <span data-ttu-id="6fbd0-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-317">'Razor'</span></span>
- <span data-ttu-id="6fbd0-318">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-319">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-320">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-321">'Identity'</span></span>
- <span data-ttu-id="6fbd0-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-323">'Razor'</span></span>
- <span data-ttu-id="6fbd0-324">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-324">'SignalR' uid:</span></span> 

<span data-ttu-id="6fbd0-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Firma Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="6fbd0-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="6fbd0-326">W przykładowej aplikacji `userinfo.profile` zakres Google jest automatycznie dodawany przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6fbd0-327">Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6fbd0-328">W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6fbd0-329">Mapuj klucze danych użytkownika i Utwórz oświadczenia</span><span class="sxs-lookup"><span data-stu-id="6fbd0-329">Map user data keys and create claims</span></span>

<span data-ttu-id="6fbd0-330">W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6fbd0-331">Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6fbd0-332">Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6fbd0-333">W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6fbd0-334">W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6fbd0-335">W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6fbd0-336">Domyślnie oświadczenia użytkownika są przechowywane w pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="6fbd0-337">Jeśli plik cookie uwierzytelniania jest zbyt duży, może to spowodować niepowodzenie aplikacji, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6fbd0-338">Przeglądarka wykryje, że nagłówek pliku cookie jest zbyt długi.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="6fbd0-339">Całkowity rozmiar żądania jest zbyt duży.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="6fbd0-340">Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6fbd0-341">Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6fbd0-342">Użyj niestandardowego <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> dla oprogramowania pośredniczącego uwierzytelniania plików cookie, <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6fbd0-343">Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6fbd0-344">Zapisz token dostępu</span><span class="sxs-lookup"><span data-stu-id="6fbd0-344">Save the access token</span></span>

<span data-ttu-id="6fbd0-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6fbd0-346">`SaveTokens`jest domyślnie ustawiona na wartość, `false` Aby zmniejszyć rozmiar ostatniego pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="6fbd0-347">Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6fbd0-348">Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6fbd0-349">Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6fbd0-350">Jak dodać dodatkowe tokeny niestandardowe</span><span class="sxs-lookup"><span data-stu-id="6fbd0-350">How to add additional custom tokens</span></span>

<span data-ttu-id="6fbd0-351">Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6fbd0-352">Tworzenie i Dodawanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6fbd0-352">Creating and adding claims</span></span>

<span data-ttu-id="6fbd0-353">Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6fbd0-354">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6fbd0-355">Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6fbd0-356">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6fbd0-357">Usuwanie akcji oświadczeń i oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6fbd0-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="6fbd0-358">[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6fbd0-359">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6fbd0-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6fbd0-361">Przykładowe dane wyjściowe aplikacji</span><span class="sxs-lookup"><span data-stu-id="6fbd0-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6fbd0-362">Aplikacja ASP.NET Core może nawiązać dodatkowe oświadczenia i tokeny od zewnętrznych dostawców uwierzytelniania, takich jak Facebook, Google, Microsoft i Twitter.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="6fbd0-363">Każdy dostawca ujawnia różne informacje o użytkownikach na jego platformie, ale wzorzec do odbioru i przekształcania danych użytkownika w dodatkowe oświadczenia jest taki sam.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="6fbd0-364">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6fbd0-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6fbd0-365">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="6fbd0-365">Prerequisites</span></span>

<span data-ttu-id="6fbd0-366">Zdecyduj, które zewnętrzne dostawcy uwierzytelniania mają być obsługiwane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="6fbd0-367">Dla każdego dostawcy Zarejestruj aplikację i uzyskaj identyfikator klienta i klucz tajny klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="6fbd0-368">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="6fbd0-369">Przykładowa aplikacja używa [dostawcy uwierzytelniania Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="6fbd0-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="6fbd0-370">Ustawianie identyfikatora klienta i klucza tajnego klienta</span><span class="sxs-lookup"><span data-stu-id="6fbd0-370">Set the client ID and client secret</span></span>

<span data-ttu-id="6fbd0-371">Dostawca uwierzytelniania OAuth ustanawia relację zaufania z aplikacją przy użyciu identyfikatora klienta i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="6fbd0-372">Wartości identyfikator klienta i klucz tajny klienta są tworzone dla aplikacji przez zewnętrznego dostawcę uwierzytelniania, gdy aplikacja jest zarejestrowana w dostawcy.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="6fbd0-373">Każdy dostawca zewnętrzny, którego używa aplikacja, musi być skonfigurowany niezależnie od identyfikatora klienta dostawcy i klucza tajnego klienta.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="6fbd0-374">Aby uzyskać więcej informacji, zobacz tematy dotyczące zewnętrznego dostawcy uwierzytelniania, które dotyczą Twojego scenariusza:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="6fbd0-375">Uwierzytelnianie przy użyciu usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="6fbd0-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="6fbd0-376">Uwierzytelnianie przy użyciu usługi Google</span><span class="sxs-lookup"><span data-stu-id="6fbd0-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="6fbd0-377">Uwierzytelnianie firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6fbd0-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="6fbd0-378">Uwierzytelnianie przy użyciu usługi Twitter</span><span class="sxs-lookup"><span data-stu-id="6fbd0-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="6fbd0-379">Inni dostawcy uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6fbd0-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="6fbd0-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="6fbd0-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="6fbd0-381">Przykładowa aplikacja konfiguruje dostawcę uwierzytelniania Google przy użyciu identyfikatora klienta i klucza tajnego klienta dostarczonego przez firmę Google:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="6fbd0-382">Ustanów zakres uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6fbd0-382">Establish the authentication scope</span></span>

<span data-ttu-id="6fbd0-383">Określ listę uprawnień do pobrania od dostawcy, określając <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="6fbd0-384">Zakresy uwierzytelniania dla typowych dostawców zewnętrznych są wyświetlane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="6fbd0-385">Dostawca</span><span class="sxs-lookup"><span data-stu-id="6fbd0-385">Provider</span></span>  | <span data-ttu-id="6fbd0-386">Zakres</span><span class="sxs-lookup"><span data-stu-id="6fbd0-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="6fbd0-387">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-388">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-389">'Identity'</span></span>
- <span data-ttu-id="6fbd0-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-391">'Razor'</span></span>
- <span data-ttu-id="6fbd0-392">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-393">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-394">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-395">'Identity'</span></span>
- <span data-ttu-id="6fbd0-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-397">'Razor'</span></span>
- <span data-ttu-id="6fbd0-398">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-398">'SignalR' uid:</span></span> 

<span data-ttu-id="6fbd0-399">----- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-400">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-401">'Identity'</span></span>
- <span data-ttu-id="6fbd0-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-403">'Razor'</span></span>
- <span data-ttu-id="6fbd0-404">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-405">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-406">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-407">'Identity'</span></span>
- <span data-ttu-id="6fbd0-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-409">'Razor'</span></span>
- <span data-ttu-id="6fbd0-410">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-411">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-412">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-413">'Identity'</span></span>
- <span data-ttu-id="6fbd0-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-415">'Razor'</span></span>
- <span data-ttu-id="6fbd0-416">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-417">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-418">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-419">'Identity'</span></span>
- <span data-ttu-id="6fbd0-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-421">'Razor'</span></span>
- <span data-ttu-id="6fbd0-422">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-423">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-424">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-425">'Identity'</span></span>
- <span data-ttu-id="6fbd0-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-427">'Razor'</span></span>
- <span data-ttu-id="6fbd0-428">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-429">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-430">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-431">'Identity'</span></span>
- <span data-ttu-id="6fbd0-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-433">'Razor'</span></span>
- <span data-ttu-id="6fbd0-434">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-435">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-436">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-437">'Identity'</span></span>
- <span data-ttu-id="6fbd0-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-439">'Razor'</span></span>
- <span data-ttu-id="6fbd0-440">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-441">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-442">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-443">'Identity'</span></span>
- <span data-ttu-id="6fbd0-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-445">'Razor'</span></span>
- <span data-ttu-id="6fbd0-446">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-447">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-448">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-449">'Identity'</span></span>
- <span data-ttu-id="6fbd0-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-451">'Razor'</span></span>
- <span data-ttu-id="6fbd0-452">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-453">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-454">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-455">'Identity'</span></span>
- <span data-ttu-id="6fbd0-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-457">'Razor'</span></span>
- <span data-ttu-id="6fbd0-458">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-459">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-460">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-461">'Identity'</span></span>
- <span data-ttu-id="6fbd0-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-463">'Razor'</span></span>
- <span data-ttu-id="6fbd0-464">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-465">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-466">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-467">'Identity'</span></span>
- <span data-ttu-id="6fbd0-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-469">'Razor'</span></span>
- <span data-ttu-id="6fbd0-470">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-471">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-472">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-473">'Identity'</span></span>
- <span data-ttu-id="6fbd0-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-475">'Razor'</span></span>
- <span data-ttu-id="6fbd0-476">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-477">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-478">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-479">'Identity'</span></span>
- <span data-ttu-id="6fbd0-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-481">'Razor'</span></span>
- <span data-ttu-id="6fbd0-482">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-483">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-484">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-485">'Identity'</span></span>
- <span data-ttu-id="6fbd0-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-487">'Razor'</span></span>
- <span data-ttu-id="6fbd0-488">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-489">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-490">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-491">'Identity'</span></span>
- <span data-ttu-id="6fbd0-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-493">'Razor'</span></span>
- <span data-ttu-id="6fbd0-494">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-495">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-496">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-497">'Identity'</span></span>
- <span data-ttu-id="6fbd0-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-499">'Razor'</span></span>
- <span data-ttu-id="6fbd0-500">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-501">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-502">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-503">'Identity'</span></span>
- <span data-ttu-id="6fbd0-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-505">'Razor'</span></span>
- <span data-ttu-id="6fbd0-506">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-507">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-508">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-509">'Identity'</span></span>
- <span data-ttu-id="6fbd0-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-511">'Razor'</span></span>
- <span data-ttu-id="6fbd0-512">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-513">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-514">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-515">'Identity'</span></span>
- <span data-ttu-id="6fbd0-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-517">'Razor'</span></span>
- <span data-ttu-id="6fbd0-518">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-519">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-520">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-521">'Identity'</span></span>
- <span data-ttu-id="6fbd0-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-523">'Razor'</span></span>
- <span data-ttu-id="6fbd0-524">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-525">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-526">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-527">'Identity'</span></span>
- <span data-ttu-id="6fbd0-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-529">'Razor'</span></span>
- <span data-ttu-id="6fbd0-530">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-531">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-532">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-533">'Identity'</span></span>
- <span data-ttu-id="6fbd0-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-535">'Razor'</span></span>
- <span data-ttu-id="6fbd0-536">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-537">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-538">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-539">'Identity'</span></span>
- <span data-ttu-id="6fbd0-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-541">'Razor'</span></span>
- <span data-ttu-id="6fbd0-542">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-543">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-544">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-545">'Identity'</span></span>
- <span data-ttu-id="6fbd0-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-547">'Razor'</span></span>
- <span data-ttu-id="6fbd0-548">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-549">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-550">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-551">'Identity'</span></span>
- <span data-ttu-id="6fbd0-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-553">'Razor'</span></span>
- <span data-ttu-id="6fbd0-554">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-555">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-556">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-557">'Identity'</span></span>
- <span data-ttu-id="6fbd0-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-559">'Razor'</span></span>
- <span data-ttu-id="6fbd0-560">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-561">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-562">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-563">'Identity'</span></span>
- <span data-ttu-id="6fbd0-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-565">'Razor'</span></span>
- <span data-ttu-id="6fbd0-566">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-567">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-568">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-569">'Identity'</span></span>
- <span data-ttu-id="6fbd0-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-571">'Razor'</span></span>
- <span data-ttu-id="6fbd0-572">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fbd0-573">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fbd0-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-574">'Blazor'</span></span>
- <span data-ttu-id="6fbd0-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-575">'Identity'</span></span>
- <span data-ttu-id="6fbd0-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fbd0-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fbd0-577">'Razor'</span></span>
- <span data-ttu-id="6fbd0-578">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-578">'SignalR' uid:</span></span> 

<span data-ttu-id="6fbd0-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Firma Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="6fbd0-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="6fbd0-580">W przykładowej aplikacji `userinfo.profile` zakres Google jest automatycznie dodawany przez platformę, gdy <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> jest wywoływana na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="6fbd0-581">Jeśli aplikacja wymaga dodatkowych zakresów, należy dodać je do opcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="6fbd0-582">W poniższym przykładzie `https://www.googleapis.com/auth/user.birthday.read` zostanie dodany zakres Google w celu pobrania urodzin użytkownika:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="6fbd0-583">Mapuj klucze danych użytkownika i Utwórz oświadczenia</span><span class="sxs-lookup"><span data-stu-id="6fbd0-583">Map user data keys and create claims</span></span>

<span data-ttu-id="6fbd0-584">W opcjach dostawcy Określ <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> lub <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> dla każdego klucza/podklucza w danych użytkownika JSON dostawcy zewnętrznego, aby tożsamość aplikacji była odczytywana podczas logowania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="6fbd0-585">Aby uzyskać więcej informacji na temat typów roszczeń, zobacz <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="6fbd0-586">Przykładowa aplikacja tworzy oświadczenia ustawień regionalnych ( `urn:google:locale` ) i obrazów ( `urn:google:picture` ) z `locale` `picture` kluczy i w danych użytkownika Google:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="6fbd0-587">W programie `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) jest zalogowany do aplikacji w usłudze <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="6fbd0-588">W procesie logowania <xref:Microsoft.AspNetCore.Identity.UserManager%601> można przechowywać `ApplicationUser` oświadczenia dla danych użytkownika dostępnych w <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="6fbd0-589">W przykładowej aplikacji `OnPostConfirmationAsync` (*Account/ExternalLogin. cshtml. cs*) określa ustawienia regionalne ( `urn:google:locale` ) i obraz ( `urn:google:picture` ) dla zalogowanego elementu, w `ApplicationUser` tym oświadczenie dla <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="6fbd0-590">Domyślnie oświadczenia użytkownika są przechowywane w pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="6fbd0-591">Jeśli plik cookie uwierzytelniania jest zbyt duży, może to spowodować niepowodzenie aplikacji, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="6fbd0-592">Przeglądarka wykryje, że nagłówek pliku cookie jest zbyt długi.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="6fbd0-593">Całkowity rozmiar żądania jest zbyt duży.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="6fbd0-594">Jeśli do przetwarzania żądań użytkowników są wymagane duże ilości danych użytkownika:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="6fbd0-595">Ogranicz liczbę i rozmiar oświadczeń użytkowników do przetwarzania żądań tylko do wymaganej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="6fbd0-596">Użyj niestandardowego <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> dla oprogramowania pośredniczącego uwierzytelniania plików cookie, <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> Aby przechowywać tożsamość między żądaniami.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="6fbd0-597">Na serwerze są zachowywane duże ilości informacji o tożsamości, a do klienta jest wysyłany tylko mały klucz identyfikatora sesji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="6fbd0-598">Zapisz token dostępu</span><span class="sxs-lookup"><span data-stu-id="6fbd0-598">Save the access token</span></span>

<span data-ttu-id="6fbd0-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>Określa, czy tokeny dostępu i odświeżania mają być przechowywane w <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po pomyślnej autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="6fbd0-600">`SaveTokens`jest domyślnie ustawiona na wartość, `false` Aby zmniejszyć rozmiar ostatniego pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="6fbd0-601">Aplikacja Przykładowa ustawia wartość `SaveTokens` na `true` w <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="6fbd0-602">Gdy jest `OnPostConfirmationAsync` wykonywane, należy przechowywać token dostępu ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z zewnętrznego dostawcy w `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="6fbd0-603">Aplikacja Przykładowa zapisuje token dostępu w programie `OnPostConfirmationAsync` (Rejestracja nowego użytkownika) i `OnGetCallbackAsync` (wcześniej zarejestrowany użytkownik) w ramach *konta/ExternalLogin. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="6fbd0-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="6fbd0-604">Jak dodać dodatkowe tokeny niestandardowe</span><span class="sxs-lookup"><span data-stu-id="6fbd0-604">How to add additional custom tokens</span></span>

<span data-ttu-id="6fbd0-605">Aby zademonstrować sposób dodawania tokenu niestandardowego, który jest przechowywany w ramach programu `SaveTokens` , przykładowa aplikacja dodaje element <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> z bieżącą <xref:System.DateTime> [AuthenticationToken.Nameą](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="6fbd0-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="6fbd0-606">Tworzenie i Dodawanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6fbd0-606">Creating and adding claims</span></span>

<span data-ttu-id="6fbd0-607">Struktura zawiera typowe akcje i metody rozszerzające do tworzenia i dodawania oświadczeń do kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="6fbd0-608">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> i <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="6fbd0-609">Użytkownicy mogą definiować niestandardowe akcje, wyprowadzając z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> i implementując metodę abstrakcyjną <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="6fbd0-610">Aby uzyskać więcej informacji, zobacz <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="6fbd0-611">Usuwanie akcji oświadczeń i oświadczeń</span><span class="sxs-lookup"><span data-stu-id="6fbd0-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="6fbd0-612">[ClaimActionCollection. Remove (ciąg)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) usuwa wszystkie akcje roszczeń dla danego elementu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekcji.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="6fbd0-613">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) usuwa wniosek <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> pochodzący z tożsamości.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="6fbd0-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>jest używany przede wszystkim z [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) w celu usunięcia oświadczeń generowanych przez protokół.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="6fbd0-615">Przykładowe dane wyjściowe aplikacji</span><span class="sxs-lookup"><span data-stu-id="6fbd0-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6fbd0-616">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6fbd0-616">Additional resources</span></span>

* <span data-ttu-id="6fbd0-617">[aplikacja AspNetCore inżynieryjna/SocialSample inżynierii](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): połączona Przykładowa aplikacja znajduje się w gałęzi inżyniera programu [dotnet/AspNetCore w witrynie GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="6fbd0-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="6fbd0-618">`master`Gałąź zawiera kod w ramach aktywnego programowania dla następnej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fbd0-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="6fbd0-619">Aby wyświetlić wersję przykładowej aplikacji dla wydanej wersji ASP.NET Core, Użyj listy rozwijanej **rozgałęzienie** , aby wybrać gałąź wydania (na przykład `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="6fbd0-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
