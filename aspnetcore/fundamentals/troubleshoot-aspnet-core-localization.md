---
title: Rozwiązywanie problemów z lokalizacją ASP.NET Core
author: hishamco
description: Dowiedz się, jak zdiagnozować problemy z lokalizacją w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
no-loc:
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 1cb7fb61063bf888819e940476f27c3adafa24de
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629928"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="6f07c-103">Rozwiązywanie problemów z lokalizacją ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f07c-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="6f07c-104">Według [Hisham bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="6f07c-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="6f07c-105">Ten artykuł zawiera instrukcje dotyczące sposobu diagnozowania ASP.NET Core problemów z lokalizacją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6f07c-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="6f07c-106">Problemy z konfiguracją lokalizacji</span><span class="sxs-lookup"><span data-stu-id="6f07c-106">Localization configuration issues</span></span>

<span data-ttu-id="6f07c-107">**Kolejność oprogramowania pośredniczącego lokalizacji**</span><span class="sxs-lookup"><span data-stu-id="6f07c-107">**Localization middleware order**</span></span>  
<span data-ttu-id="6f07c-108">Aplikacja może nie być zlokalizowana, ponieważ oprogramowanie pośredniczące nie jest uporządkowane zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="6f07c-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="6f07c-109">Aby rozwiązać ten problem, upewnij się, że oprogramowanie pośredniczące do lokalizowania jest zarejestrowane przed oprogramowaniem pośredniczącym MVC.</span><span class="sxs-lookup"><span data-stu-id="6f07c-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="6f07c-110">W przeciwnym razie nie zostanie zastosowane oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="6f07c-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="6f07c-111">**Nie znaleziono ścieżki zasobów lokalizacji**</span><span class="sxs-lookup"><span data-stu-id="6f07c-111">**Localization resources path not found**</span></span>

<span data-ttu-id="6f07c-112">**Obsługiwane kultury w RequestCultureProvider nie pasują do zarejestrowanego wystąpienia**</span><span class="sxs-lookup"><span data-stu-id="6f07c-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="6f07c-113">Problemy z nazewnictwem plików zasobów</span><span class="sxs-lookup"><span data-stu-id="6f07c-113">Resource file naming issues</span></span>

<span data-ttu-id="6f07c-114">ASP.NET Core ma wstępnie zdefiniowane reguły i wskazówki dotyczące nazewnictwa plików zasobów lokalizacyjnych, które opisano szczegółowo w [tym miejscu](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="6f07c-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="6f07c-115">Brakujące zasoby</span><span class="sxs-lookup"><span data-stu-id="6f07c-115">Missing resources</span></span>

<span data-ttu-id="6f07c-116">Typowe przyczyny nieznalezienia zasobów obejmują:</span><span class="sxs-lookup"><span data-stu-id="6f07c-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="6f07c-117">Nazwy zasobów są błędne w `resx` pliku lub w żądaniu lokalizatora.</span><span class="sxs-lookup"><span data-stu-id="6f07c-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="6f07c-118">Brak zasobu `resx` w przypadku niektórych języków, ale istnieje w innych.</span><span class="sxs-lookup"><span data-stu-id="6f07c-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="6f07c-119">Jeśli nadal występują problemy, sprawdź komunikaty dziennika lokalizacji (które znajdują się na `Debug` poziomie dziennika), aby uzyskać więcej informacji na temat brakujących zasobów.</span><span class="sxs-lookup"><span data-stu-id="6f07c-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="6f07c-120">_**Wskazówka:** W przypadku korzystania `CookieRequestCultureProvider` z programu Sprawdź, czy pojedyncze cudzysłowy nie są używane z kulturami wewnątrz cookie wartości lokalizacji. Na przykład `c='en-UK'|uic='en-US'` jest nieprawidłową cookie wartością, podczas gdy `c=en-UK|uic=en-US` jest prawidłowy._</span><span class="sxs-lookup"><span data-stu-id="6f07c-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="6f07c-121">Zasoby & problemy z bibliotekami klas</span><span class="sxs-lookup"><span data-stu-id="6f07c-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="6f07c-122">ASP.NET Core domyślnie oferuje sposób zezwalania bibliotekom klas na Znajdowanie plików zasobów za pośrednictwem [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="6f07c-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="6f07c-123">Typowe problemy związane z bibliotekami klas obejmują:</span><span class="sxs-lookup"><span data-stu-id="6f07c-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="6f07c-124">Brak `ResourceLocationAttribute` elementu w bibliotece klas uniemożliwi `ResourceManagerStringLocalizerFactory` odnajdywanie zasobów.</span><span class="sxs-lookup"><span data-stu-id="6f07c-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="6f07c-125">Nazewnictwo plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="6f07c-125">Resource file naming.</span></span> <span data-ttu-id="6f07c-126">Aby uzyskać więcej informacji, zobacz sekcję [problemy związane z nazewnictwem plików zasobów](#resource-file-naming-issues) .</span><span class="sxs-lookup"><span data-stu-id="6f07c-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="6f07c-127">Zmiana głównej przestrzeni nazw biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="6f07c-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="6f07c-128">Aby uzyskać więcej informacji, zobacz sekcję [problemy dotyczące głównej przestrzeni nazw](#root-namespace-issues) .</span><span class="sxs-lookup"><span data-stu-id="6f07c-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="6f07c-129">CustomRequestCultureProvider nie działa zgodnie z oczekiwaniami</span><span class="sxs-lookup"><span data-stu-id="6f07c-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="6f07c-130">`RequestLocalizationOptions`Klasa ma trzech dostawców domyślnych:</span><span class="sxs-lookup"><span data-stu-id="6f07c-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="6f07c-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umożliwia dostosowanie sposobu, w jaki kultura lokalizacji jest udostępniana w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6f07c-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="6f07c-132">`CustomRequestCultureProvider`Jest używany, gdy dostawcy domyślnie nie spełniają Twoich wymagań.</span><span class="sxs-lookup"><span data-stu-id="6f07c-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="6f07c-133">Typowy powód niestandardowego dostawcy nie działa prawidłowo, ponieważ nie jest to pierwszy dostawca na `RequestCultureProviders` liście.</span><span class="sxs-lookup"><span data-stu-id="6f07c-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="6f07c-134">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="6f07c-134">To resolve this issue:</span></span>

- <span data-ttu-id="6f07c-135">Wstaw dostawcę niestandardowego na pozycji 0 na `RequestCultureProviders` liście w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="6f07c-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="6f07c-136">Użyj `AddInitialRequestCultureProvider` metody rozszerzenia, aby ustawić dostawcę niestandardowego jako dostawcę początkowego.</span><span class="sxs-lookup"><span data-stu-id="6f07c-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="6f07c-137">Problemy z główną przestrzenią nazw</span><span class="sxs-lookup"><span data-stu-id="6f07c-137">Root Namespace issues</span></span>

<span data-ttu-id="6f07c-138">Gdy główna przestrzeń nazw zestawu różni się od nazwy zestawu, lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="6f07c-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="6f07c-139">Aby uniknąć tego problemu, użyj [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), który jest szczegółowo opisany [tutaj](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="6f07c-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="6f07c-140">Taka sytuacja może wystąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="6f07c-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="6f07c-141">Na przykład `my-project-name.csproj` użyje głównej przestrzeni nazw `my_project_name` i nazwy zestawu `my-project-name` prowadzącej do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="6f07c-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="6f07c-142">Akcja kompilacji & zasobów</span><span class="sxs-lookup"><span data-stu-id="6f07c-142">Resources & Build Action</span></span>

<span data-ttu-id="6f07c-143">W przypadku używania plików zasobów do lokalizacji należy pamiętać, że mają one odpowiednią akcję kompilacji.</span><span class="sxs-lookup"><span data-stu-id="6f07c-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="6f07c-144">Powinny to być **zasoby osadzone**, w przeciwnym razie `ResourceStringLocalizer` nie można znaleźć tych zasobów.</span><span class="sxs-lookup"><span data-stu-id="6f07c-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
