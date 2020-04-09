---
title: Rozwiązywanie problemów z lokalizacją ASP.NET rdzenia
author: hishamco
description: Dowiedz się, jak zdiagnozować problemy z lokalizacją w aplikacjach ASP.NET Core.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660378"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="c998e-103">Rozwiązywanie problemów z lokalizacją ASP.NET rdzenia</span><span class="sxs-lookup"><span data-stu-id="c998e-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="c998e-104">Przez [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="c998e-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="c998e-105">Ten artykuł zawiera instrukcje dotyczące diagnozowania problemów z lokalizacją aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c998e-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="c998e-106">Problemy z konfiguracją lokalizacji</span><span class="sxs-lookup"><span data-stu-id="c998e-106">Localization configuration issues</span></span>

<span data-ttu-id="c998e-107">**Kolejność oprogramowania pośredniczącego lokalizacji**</span><span class="sxs-lookup"><span data-stu-id="c998e-107">**Localization middleware order**</span></span>  
<span data-ttu-id="c998e-108">Aplikacja może nie lokalizować, ponieważ oprogramowanie pośredniczące lokalizacji nie jest uporządkowane zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="c998e-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="c998e-109">Aby rozwiązać ten problem, upewnij się, że oprogramowanie pośredniczące lokalizacji jest zarejestrowane przed oprogramowaniem pośredniczącym MVC.</span><span class="sxs-lookup"><span data-stu-id="c998e-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="c998e-110">W przeciwnym razie oprogramowanie pośredniczące lokalizacji nie jest stosowane.</span><span class="sxs-lookup"><span data-stu-id="c998e-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="c998e-111">**Nie znaleziono ścieżki zasobów lokalizacyjnych**</span><span class="sxs-lookup"><span data-stu-id="c998e-111">**Localization resources path not found**</span></span>

<span data-ttu-id="c998e-112">**Obsługiwane kultury w RequestCultureProvider nie pasują do zarejestrowanych raz**</span><span class="sxs-lookup"><span data-stu-id="c998e-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="c998e-113">Problemy z nazewnictwem plików zasobów</span><span class="sxs-lookup"><span data-stu-id="c998e-113">Resource file naming issues</span></span>

<span data-ttu-id="c998e-114">ASP.NET Core ma wstępnie zdefiniowane reguły i wytyczne dotyczące nazewnictwa plików zasobów lokalizacyjnych, które są szczegółowo opisane w [tym miejscu](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span><span class="sxs-lookup"><span data-stu-id="c998e-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="c998e-115">Brakujące zasoby</span><span class="sxs-lookup"><span data-stu-id="c998e-115">Missing resources</span></span>

<span data-ttu-id="c998e-116">Typowe przyczyny nieuleczania zasobów obejmują:</span><span class="sxs-lookup"><span data-stu-id="c998e-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="c998e-117">Nazwy zasobów są błędnie wpisane w `resx` pliku lub żądania lokalizatora.</span><span class="sxs-lookup"><span data-stu-id="c998e-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="c998e-118">Zasób brakuje `resx` w niektórych językach, ale istnieje w innych.</span><span class="sxs-lookup"><span data-stu-id="c998e-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="c998e-119">Jeśli nadal występują problemy, sprawdź komunikaty dziennika lokalizacji `Debug` (które znajdują się na poziomie dziennika), aby uzyskać więcej informacji na temat brakujących zasobów.</span><span class="sxs-lookup"><span data-stu-id="c998e-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="c998e-120">_**Podpowiedź:** Podczas `CookieRequestCultureProvider`korzystania z , sprawdź pojedyncze cudzysłowy nie są używane z kultur wewnątrz wartości pliku cookie lokalizacji. Na przykład `c='en-UK'|uic='en-US'` jest nieprawidłową wartością pliku cookie, gdy `c=en-UK|uic=en-US` jest prawidłowa._</span><span class="sxs-lookup"><span data-stu-id="c998e-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="c998e-121">Problemy z bibliotekami & zasobów</span><span class="sxs-lookup"><span data-stu-id="c998e-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="c998e-122">ASP.NET Core domyślnie umożliwia bibliotekom klas znajdowanie plików zasobów za pośrednictwem [atrybutu ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span><span class="sxs-lookup"><span data-stu-id="c998e-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="c998e-123">Typowe problemy z bibliotekami klas obejmują:</span><span class="sxs-lookup"><span data-stu-id="c998e-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="c998e-124">Brak `ResourceLocationAttribute` w bibliotece klas `ResourceManagerStringLocalizerFactory` uniemożliwi odnajdowanie zasobów.</span><span class="sxs-lookup"><span data-stu-id="c998e-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="c998e-125">Nazewnictwo plików zasobów.</span><span class="sxs-lookup"><span data-stu-id="c998e-125">Resource file naming.</span></span> <span data-ttu-id="c998e-126">Aby uzyskać więcej informacji, zobacz Sekcja [Problemy z nazewnictwem plików zasobów.](#resource-file-naming-issues)</span><span class="sxs-lookup"><span data-stu-id="c998e-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="c998e-127">Zmiana głównego obszaru nazw biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="c998e-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="c998e-128">Aby uzyskać więcej informacji, zobacz [sekcję Problemy z głównym obszarem nazw.](#root-namespace-issues)</span><span class="sxs-lookup"><span data-stu-id="c998e-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="c998e-129">CustomRequestCultureProvider nie działa zgodnie z oczekiwaniami</span><span class="sxs-lookup"><span data-stu-id="c998e-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="c998e-130">Klasa `RequestLocalizationOptions` ma trzech domyślnych dostawców:</span><span class="sxs-lookup"><span data-stu-id="c998e-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="c998e-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) umożliwia dostosowanie, jak kultura lokalizacji jest dostępna w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c998e-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="c998e-132">Jest `CustomRequestCultureProvider` używany, gdy dostawcy domyślni nie spełniają twoich wymagań.</span><span class="sxs-lookup"><span data-stu-id="c998e-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="c998e-133">Częstym powodem, dla którego dostawca niestandardowy nie działa poprawnie, `RequestCultureProviders` jest to, że nie jest pierwszym dostawcą na liście.</span><span class="sxs-lookup"><span data-stu-id="c998e-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="c998e-134">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="c998e-134">To resolve this issue:</span></span>

- <span data-ttu-id="c998e-135">Wstaw dostawcę niestandardowego w `RequestCultureProviders` pozycji 0 na liście jako następujące:</span><span class="sxs-lookup"><span data-stu-id="c998e-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

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

- <span data-ttu-id="c998e-136">Użyj `AddInitialRequestCultureProvider` metody rozszerzenia, aby ustawić dostawcę niestandardowego jako dostawcę początkowego.</span><span class="sxs-lookup"><span data-stu-id="c998e-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="c998e-137">Problemy z głównym obszarem nazw</span><span class="sxs-lookup"><span data-stu-id="c998e-137">Root Namespace issues</span></span>

<span data-ttu-id="c998e-138">Gdy główny obszar nazw zestawu jest inny niż nazwa zestawu, lokalizacja nie działa domyślnie.</span><span class="sxs-lookup"><span data-stu-id="c998e-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="c998e-139">Aby uniknąć tego problemu, użyj [rootnamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), który jest szczegółowo opisany [tutaj](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span><span class="sxs-lookup"><span data-stu-id="c998e-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="c998e-140">Może to nastąpić, gdy nazwa projektu nie jest prawidłowym identyfikatorem platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="c998e-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="c998e-141">Na `my-project-name.csproj` przykład użyje głównego `my_project_name` obszaru nazw `my-project-name` i nazwy zestawu prowadzącego do tego błędu.</span><span class="sxs-lookup"><span data-stu-id="c998e-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="c998e-142">Zasoby & akcji kompilacji</span><span class="sxs-lookup"><span data-stu-id="c998e-142">Resources & Build Action</span></span>

<span data-ttu-id="c998e-143">Jeśli używasz plików zasobów do lokalizacji, ważne jest, że mają one odpowiednią akcję kompilacji.</span><span class="sxs-lookup"><span data-stu-id="c998e-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="c998e-144">Powinny one być **osadzone zasobów,** w `ResourceStringLocalizer` przeciwnym razie nie jest w stanie znaleźć te zasoby.</span><span class="sxs-lookup"><span data-stu-id="c998e-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
