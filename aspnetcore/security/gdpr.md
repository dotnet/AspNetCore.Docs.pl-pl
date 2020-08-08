---
title: Obsługa Ogólne rozporządzenie o ochronie danych (Rodo) w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak uzyskać dostęp do punktów rozszerzenia Rodo w aplikacji sieci Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 6392a22e316f903da18cd1a91d1eb779d8dde1b3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020018"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9e459-103">Obsługa Ogólne rozporządzenie o ochronie danych UE (Rodo) w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e459-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9e459-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9e459-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9e459-105">ASP.NET Core udostępnia interfejsy API i szablony, które pomagają spełnić niektóre wymagania dotyczące [ogólne rozporządzenie o ochronie danych UE (Rodo)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="9e459-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9e459-106">Szablony projektu obejmują punkty rozszerzenia i użyto metod zastępczych znaczników, które można zamienić na zasady zachowania poufności i cookie używania zasad.</span><span class="sxs-lookup"><span data-stu-id="9e459-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9e459-107">Widok Pages */privacy. cshtml* lub *widoki/Home/privacy. cshtml* zawiera stronę zawierającą szczegółowe informacje o zasadach zachowania poufności informacji.</span><span class="sxs-lookup"><span data-stu-id="9e459-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9e459-108">Aby włączyć domyślną cookie funkcję wyrażania zgody, która została znaleziona w szablonach ASP.NET Core 2,2 w aplikacji ASP.NET Core 3,0, wygenerowana przez szablon:</span><span class="sxs-lookup"><span data-stu-id="9e459-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9e459-109">Dodaj `using Microsoft.AspNetCore.Http` do listy dyrektyw using.</span><span class="sxs-lookup"><span data-stu-id="9e459-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9e459-110">Dodawanie [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) do `Startup.ConfigureServices` [ Cookie zasad i używanie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) ich w celu `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9e459-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9e459-111">Dodaj cookie częściowo zgodę do pliku *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9e459-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9e459-112">Dodaj plik \* \_ Cookie ConsentPartial. cshtml\* do projektu:</span><span class="sxs-lookup"><span data-stu-id="9e459-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="9e459-113">Wybierz ASP.NET Core wersję 2,2 tego artykułu, aby przeczytać o cookie funkcji wyrażania zgody.</span><span class="sxs-lookup"><span data-stu-id="9e459-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9e459-114">Szablony projektu obejmują punkty rozszerzenia i użyto metod zastępczych znaczników, które można zamienić na zasady zachowania poufności i cookie używania zasad.</span><span class="sxs-lookup"><span data-stu-id="9e459-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9e459-115">cookieFunkcja wyrażania zgody umożliwia poproszenie użytkowników o zgodę na przechowywanie informacji osobistych (i śledzenie ich).</span><span class="sxs-lookup"><span data-stu-id="9e459-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9e459-116">Jeśli użytkownik nie wyraził zgody na zbieranie danych, a aplikacja ma [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) ustawiony na `true` , nieistotne cookie s nie są wysyłane do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9e459-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="9e459-117">Cookies może być oznaczony jako istotny.</span><span class="sxs-lookup"><span data-stu-id="9e459-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="9e459-118">Podstawowe cookie elementy s są wysyłane do przeglądarki nawet wtedy, gdy użytkownik nie wyraził zgody i śledzenie jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9e459-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9e459-119">[TempData i Session cookie s](#tempdata) nie działają, gdy śledzenie jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9e459-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9e459-120">Strona [ Identity Zarządzanie](#pd) zawiera link umożliwiający pobranie i usunięcie danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9e459-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9e459-121">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umożliwia przetestowanie większości punktów rozszerzenia Rodo i interfejsów API dodanych do szablonów ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9e459-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9e459-122">Instrukcje dotyczące testowania można znaleźć w pliku [README](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .</span><span class="sxs-lookup"><span data-stu-id="9e459-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9e459-123">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e459-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9e459-124">ASP.NET Core obsługa Rodo w kodzie wygenerowanym przez szablon</span><span class="sxs-lookup"><span data-stu-id="9e459-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="9e459-125">RazorStrony i projekty MVC utworzone przy użyciu szablonów projektu obejmują następujące wsparcie Rodo:</span><span class="sxs-lookup"><span data-stu-id="9e459-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9e459-126">[ Cookie Zasady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) i use są ustawiane w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="9e459-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9e459-127">[Widok częściowy](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) \* \_ Cookie ConsentPartial. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="9e459-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9e459-128">W tym pliku znajduje się przycisk **Akceptuj** .</span><span class="sxs-lookup"><span data-stu-id="9e459-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9e459-129">Gdy użytkownik kliknie przycisk **Akceptuj** , wyrażasz zgodę na przechowywanie cookie s.</span><span class="sxs-lookup"><span data-stu-id="9e459-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="9e459-130">Widok Pages */privacy. cshtml* lub *widoki/Home/privacy. cshtml* zawiera stronę zawierającą szczegółowe informacje o zasadach zachowania poufności informacji.</span><span class="sxs-lookup"><span data-stu-id="9e459-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9e459-131">Plik \* \_ Cookie ConsentPartial. cshtml\* generuje link do strony prywatność.</span><span class="sxs-lookup"><span data-stu-id="9e459-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9e459-132">W przypadku aplikacji utworzonych przy użyciu poszczególnych kont użytkowników Strona Zarządzanie zawiera linki umożliwiające pobranie i usunięcie [osobistych danych użytkownika](#pd).</span><span class="sxs-lookup"><span data-stu-id="9e459-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="9e459-133">CookieZasady PolicyOptions i użycia Cookie</span><span class="sxs-lookup"><span data-stu-id="9e459-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="9e459-134">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) są inicjowane w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9e459-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9e459-135">[Użyj Cookie Zasady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) są wywoływane w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9e459-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9e459-136">\_CookieWidok częściowy ConsentPartial. cshtml</span><span class="sxs-lookup"><span data-stu-id="9e459-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9e459-137">Widok częściowy \* \_ Cookie ConsentPartial. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="9e459-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="9e459-138">Ta część częściowa:</span><span class="sxs-lookup"><span data-stu-id="9e459-138">This partial:</span></span>

* <span data-ttu-id="9e459-139">Uzyskuje stan śledzenia dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9e459-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9e459-140">Jeśli aplikacja jest skonfigurowana do wymagania zgody, użytkownik musi wyrazić zgodę przed rozpoczęciem cookie śledzenia.</span><span class="sxs-lookup"><span data-stu-id="9e459-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="9e459-141">Jeśli jest wymagana zgoda, cookie panel zgody jest ustalany w górnej części paska nawigacyjnego utworzonego przez plik \* \_ Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="9e459-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9e459-142">Udostępnia element HTML `<p>` do podsumowywania prywatności i cookie zasad użytkowania.</span><span class="sxs-lookup"><span data-stu-id="9e459-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="9e459-143">Zawiera link do strony lub widoku prywatności, w którym można szczegółowo zapoznać się z zasadami zachowania poufności informacji w witrynie.</span><span class="sxs-lookup"><span data-stu-id="9e459-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="9e459-144">Podstawowe cookie elementy s</span><span class="sxs-lookup"><span data-stu-id="9e459-144">Essential cookies</span></span>

<span data-ttu-id="9e459-145">Jeśli cookie nie podano zgody na przechowywanie s, cookie do przeglądarki są wysyłane tylko elementy oznaczone jako podstawowe.</span><span class="sxs-lookup"><span data-stu-id="9e459-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="9e459-146">Poniższy kod stanowi cookie zasadnicze znaczenie:</span><span class="sxs-lookup"><span data-stu-id="9e459-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="9e459-147">Dostawca TempData i stan sesji cookie s nie są istotne</span><span class="sxs-lookup"><span data-stu-id="9e459-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="9e459-148">[Dostawca TempData](xref:fundamentals/app-state#tempdata) cookie nie jest istotny.</span><span class="sxs-lookup"><span data-stu-id="9e459-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="9e459-149">Jeśli śledzenie jest wyłączone, dostawca TempData nie działa.</span><span class="sxs-lookup"><span data-stu-id="9e459-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9e459-150">Aby włączyć dostawcę TempData, gdy śledzenie jest wyłączone, Oznacz TempData cookie jako zasadniczą w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9e459-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9e459-151">[Stan sesji](xref:fundamentals/app-state) cookie s nie są istotne.</span><span class="sxs-lookup"><span data-stu-id="9e459-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="9e459-152">Stan sesji nie działa, gdy śledzenie jest wyłączone.</span><span class="sxs-lookup"><span data-stu-id="9e459-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9e459-153">Poniższy kod powoduje, że cookie :</span><span class="sxs-lookup"><span data-stu-id="9e459-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9e459-154">Dane osobowe</span><span class="sxs-lookup"><span data-stu-id="9e459-154">Personal data</span></span>

<span data-ttu-id="9e459-155">ASP.NET Core aplikacje utworzone przy użyciu poszczególnych kont użytkowników zawierają kod umożliwiający pobranie i usunięcie danych osobowych.</span><span class="sxs-lookup"><span data-stu-id="9e459-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9e459-156">Wybierz nazwę użytkownika, a następnie wybierz pozycję **dane osobowe**:</span><span class="sxs-lookup"><span data-stu-id="9e459-156">Select the user name and then select **Personal data**:</span></span>

![Strona Zarządzanie danymi osobistymi](gdpr/_static/pd.png)

<span data-ttu-id="9e459-158">Uwagi:</span><span class="sxs-lookup"><span data-stu-id="9e459-158">Notes:</span></span>

* <span data-ttu-id="9e459-159">Aby wygenerować `Account/Manage` kod, zobacz [szkielet Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="9e459-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9e459-160">Linki **usuwania** i **pobierania** działają tylko na domyślnych danych tożsamości.</span><span class="sxs-lookup"><span data-stu-id="9e459-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9e459-161">Aplikacje, które tworzą niestandardowe dane użytkownika, muszą zostać rozszerzone w celu usunięcia/pobrania niestandardowych danych użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9e459-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9e459-162">Aby uzyskać więcej informacji, zobacz [Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do Identity programu ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="9e459-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9e459-163">Zapisane tokeny dla użytkownika, które są przechowywane w Identity tabeli bazy danych, `AspNetUserTokens` są usuwane, gdy użytkownik zostanie usunięty przez kaskadowe zachowanie podczas usuwania ze względu na [klucz obcy](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="9e459-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="9e459-164">[Uwierzytelnianie dostawcy zewnętrznego](xref:security/authentication/social/index), takie jak Facebook i Google, nie jest dostępne przed cookie zaakceptowaniem zasad.</span><span class="sxs-lookup"><span data-stu-id="9e459-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9e459-165">Szyfrowanie danych magazynowanych</span><span class="sxs-lookup"><span data-stu-id="9e459-165">Encryption at rest</span></span>

<span data-ttu-id="9e459-166">Niektóre bazy danych i mechanizmy magazynu umożliwiają szyfrowanie w spoczynku.</span><span class="sxs-lookup"><span data-stu-id="9e459-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9e459-167">Szyfrowanie w spoczynku:</span><span class="sxs-lookup"><span data-stu-id="9e459-167">Encryption at rest:</span></span>

* <span data-ttu-id="9e459-168">Szyfruje przechowywane dane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="9e459-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9e459-169">Szyfruje bez konfiguracji, programowania lub innej pracy dla oprogramowania, które uzyskuje dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="9e459-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9e459-170">Jest najłatwiejszym i najbezpieczniejszą opcją.</span><span class="sxs-lookup"><span data-stu-id="9e459-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9e459-171">Umożliwia bazie danych zarządzanie kluczami i szyfrowaniem.</span><span class="sxs-lookup"><span data-stu-id="9e459-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9e459-172">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9e459-172">For example:</span></span>

* <span data-ttu-id="9e459-173">Program Microsoft SQL i usługa Azure SQL zapewniają [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="9e459-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9e459-174">Usługa SQL Azure domyślnie szyfruje bazę danych</span><span class="sxs-lookup"><span data-stu-id="9e459-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9e459-175">[Obiekty blob, pliki, tabele i queue storage platformy Azure domyślnie są szyfrowane](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="9e459-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9e459-176">W przypadku baz danych, które nie zapewniają wbudowanego szyfrowania w spoczynku, może być możliwe użycie szyfrowania dysków w celu zapewnienia tej samej ochrony.</span><span class="sxs-lookup"><span data-stu-id="9e459-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9e459-177">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9e459-177">For example:</span></span>

* [<span data-ttu-id="9e459-178">Funkcja BitLocker dla systemu Windows Server</span><span class="sxs-lookup"><span data-stu-id="9e459-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9e459-179">W systemie Linux:</span><span class="sxs-lookup"><span data-stu-id="9e459-179">Linux:</span></span>
  * [<span data-ttu-id="9e459-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="9e459-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9e459-181">[EncFs](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="9e459-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e459-182">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9e459-182">Additional resources</span></span>

* [<span data-ttu-id="9e459-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9e459-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="9e459-184">Rodo — Dodawanie przycisku odwołaj zgodę w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e459-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
