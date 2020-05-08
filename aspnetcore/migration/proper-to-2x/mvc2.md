---
title: Migrowanie z ASP.NET do ASP.NET Core 2,0
author: isaac2004
description: Otrzymuj wskazówki dotyczące migrowania istniejących aplikacji ASP.NET MVC lub Web API do ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc2
ms.openlocfilehash: 98b93731d40b47be8d7d5050afc7c74d25a7e1dc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776275"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="fbf23-103">Migrowanie z ASP.NET do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="fbf23-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="fbf23-104">Autor [Tomasz Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="fbf23-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="fbf23-105">Ten artykuł służy jako Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET do ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="fbf23-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fbf23-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="fbf23-106">Prerequisites</span></span>

<span data-ttu-id="fbf23-107">Zainstaluj **jedną** z następujących czynności z programu [.NET downloads: Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="fbf23-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="fbf23-108">Zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="fbf23-108">.NET Core SDK</span></span>
* <span data-ttu-id="fbf23-109">Program Visual Studio dla systemu Windows</span><span class="sxs-lookup"><span data-stu-id="fbf23-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="fbf23-110">**ASP.NET i programowanie aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="fbf23-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="fbf23-111">**Tworzenie aplikacji dla wielu platform w środowisku .NET Core**</span><span class="sxs-lookup"><span data-stu-id="fbf23-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="fbf23-112">Platformy docelowe</span><span class="sxs-lookup"><span data-stu-id="fbf23-112">Target frameworks</span></span>

<span data-ttu-id="fbf23-113">Projekty ASP.NET Core 2,0 oferują deweloperom elastyczność określania platformy .NET Core, .NET Framework lub obu.</span><span class="sxs-lookup"><span data-stu-id="fbf23-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="fbf23-114">Dowiedz się, [jak wybrać platformę .NET Core i .NET Framework dla aplikacji serwerowych](/dotnet/standard/choosing-core-framework-server) , aby określić, która platforma docelowa jest najbardziej odpowiednia.</span><span class="sxs-lookup"><span data-stu-id="fbf23-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="fbf23-115">W przypadku .NET Framework określania wartości docelowej projekty muszą odwoływać się do poszczególnych pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="fbf23-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="fbf23-116">Kierowanie programu .NET Core umożliwia eliminację wielu jawnych odwołań do pakietów, dzięki czemu [ASP.NET Core 2,0.](xref:fundamentals/metapackage)</span><span class="sxs-lookup"><span data-stu-id="fbf23-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="fbf23-117">`Microsoft.AspNetCore.All` Zainstaluj pakiet w projekcie:</span><span class="sxs-lookup"><span data-stu-id="fbf23-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="fbf23-118">W przypadku użycia pakietu z aplikacją nie są wdrażane żadne pakiety, do których odwołuje się pakiet.</span><span class="sxs-lookup"><span data-stu-id="fbf23-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="fbf23-119">Magazyn środowiska uruchomieniowego .NET Core zawiera te zasoby i są wstępnie skompilowane w celu zwiększenia wydajności.</span><span class="sxs-lookup"><span data-stu-id="fbf23-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="fbf23-120">Zobacz <xref:fundamentals/metapackage> , aby uzyskać więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="fbf23-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="fbf23-121">Różnice struktury projektu</span><span class="sxs-lookup"><span data-stu-id="fbf23-121">Project structure differences</span></span>

<span data-ttu-id="fbf23-122">Format pliku *. csproj* został uproszczony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbf23-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="fbf23-123">Niektóre istotne zmiany obejmują:</span><span class="sxs-lookup"><span data-stu-id="fbf23-123">Some notable changes include:</span></span>

* <span data-ttu-id="fbf23-124">Jawne dołączenie plików nie jest konieczne, aby były uważane za część projektu.</span><span class="sxs-lookup"><span data-stu-id="fbf23-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="fbf23-125">Zmniejsza to ryzyko konfliktów scalania XML podczas pracy nad dużymi zespołami.</span><span class="sxs-lookup"><span data-stu-id="fbf23-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="fbf23-126">Nie ma żadnych odwołań opartych na identyfikatorach GUID do innych projektów, co zwiększa czytelność plików.</span><span class="sxs-lookup"><span data-stu-id="fbf23-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="fbf23-127">Plik można edytować bez zwalniania go w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="fbf23-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Edytuj opcję menu kontekstowego CSPROJ w programie Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="fbf23-129">Zastąpienie pliku Global. asax</span><span class="sxs-lookup"><span data-stu-id="fbf23-129">Global.asax file replacement</span></span>

<span data-ttu-id="fbf23-130">ASP.NET Core wprowadzono nowy mechanizm uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fbf23-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="fbf23-131">Punkt wejścia dla aplikacji ASP.NET to plik *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="fbf23-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="fbf23-132">Zadania, takie jak konfiguracja tras oraz filtrowanie i rejestrowanie obszaru, są obsługiwane w pliku *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="fbf23-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="fbf23-133">Takie podejście Couples aplikację i serwer, na który jest wdrażana w sposób, który zakłóca implementację.</span><span class="sxs-lookup"><span data-stu-id="fbf23-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="fbf23-134">W celu oddzielenia [Owin](https://owin.org/) został wprowadzony w celu zapewnienia bardziej przejrzystego sposobu używania wielu struktur.</span><span class="sxs-lookup"><span data-stu-id="fbf23-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="fbf23-135">OWIN zapewnia potok do dodawania tylko wymaganych modułów.</span><span class="sxs-lookup"><span data-stu-id="fbf23-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="fbf23-136">Środowisko hostingu wykonuje funkcję [uruchamiania](xref:fundamentals/startup) , aby skonfigurować usługi i potok żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fbf23-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="fbf23-137">`Startup`rejestruje zestaw programów pośredniczących w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fbf23-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="fbf23-138">Dla każdego żądania aplikacja wywołuje każdy składnik pośredniczący ze wskaźnikiem głównym połączonej listy z istniejącym zestawem programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="fbf23-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="fbf23-139">Każdy składnik pośredniczący może dodać jeden lub więcej programów obsługi do potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="fbf23-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="fbf23-140">Jest to realizowane przez zwrócenie odwołania do programu obsługi, który jest nowym szefem listy.</span><span class="sxs-lookup"><span data-stu-id="fbf23-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="fbf23-141">Każdy program obsługi jest odpowiedzialny za zapamiętywanie i wywoływanie kolejnej procedury obsługi na liście.</span><span class="sxs-lookup"><span data-stu-id="fbf23-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="fbf23-142">W przypadku ASP.NET Core punkt wejścia do aplikacji jest `Startup`i nie ma już zależności od elementu *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="fbf23-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="fbf23-143">W przypadku korzystania z programu OWIN z .NET Framework należy użyć podobnej do poniższej postaci potoku:</span><span class="sxs-lookup"><span data-stu-id="fbf23-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="fbf23-144">Powoduje to skonfigurowanie tras domyślnych i wartości domyślnych XmlSerialization w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="fbf23-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="fbf23-145">W razie potrzeby Dodaj inne oprogramowanie pośredniczące (ładowanie usług, ustawień konfiguracji, plików statycznych itp.).</span><span class="sxs-lookup"><span data-stu-id="fbf23-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="fbf23-146">ASP.NET Core używa podobnego podejścia, ale nie polega na OWIN do obsługi wpisu.</span><span class="sxs-lookup"><span data-stu-id="fbf23-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="fbf23-147">Zamiast tego robi to za pomocą metody *program.cs* `Main` (podobnie jak aplikacje konsolowe) i `Startup` jest ładowany w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="fbf23-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="fbf23-148">`Startup`musi zawierać `Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="fbf23-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="fbf23-149">W `Configure`programie Dodaj wymagane oprogramowanie pośredniczące do potoku.</span><span class="sxs-lookup"><span data-stu-id="fbf23-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="fbf23-150">W poniższym przykładzie (z domyślnego szablonu witryny sieci Web) kilka metod rozszerzenia służy do konfigurowania potoku z obsługą:</span><span class="sxs-lookup"><span data-stu-id="fbf23-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="fbf23-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="fbf23-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="fbf23-152">Strony błędów</span><span class="sxs-lookup"><span data-stu-id="fbf23-152">Error pages</span></span>
* <span data-ttu-id="fbf23-153">Pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="fbf23-153">Static files</span></span>
* <span data-ttu-id="fbf23-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fbf23-154">ASP.NET Core MVC</span></span>
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="fbf23-155">Host i aplikacja zostały odłączone, co zapewnia elastyczność przejścia do innej platformy w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="fbf23-155">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="fbf23-156">Aby uzyskać bardziej szczegółowe informacje dotyczące ASP.NET Core uruchamiania i oprogramowania pośredniczącego, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fbf23-156">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="fbf23-157">Przechowywanie konfiguracji</span><span class="sxs-lookup"><span data-stu-id="fbf23-157">Storing configurations</span></span>

<span data-ttu-id="fbf23-158">ASP.NET obsługuje przechowywanie ustawień.</span><span class="sxs-lookup"><span data-stu-id="fbf23-158">ASP.NET supports storing settings.</span></span> <span data-ttu-id="fbf23-159">Te ustawienia są używane na przykład w celu obsługi środowiska, w którym aplikacje zostały wdrożone.</span><span class="sxs-lookup"><span data-stu-id="fbf23-159">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="fbf23-160">Typowym celem jest przechowywanie wszystkich niestandardowych par klucz-wartość w `<appSettings>` sekcji pliku *Web. config* :</span><span class="sxs-lookup"><span data-stu-id="fbf23-160">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="fbf23-161">Aplikacje odczytują te ustawienia przy `ConfigurationManager.AppSettings` użyciu kolekcji w `System.Configuration` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="fbf23-161">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="fbf23-162">ASP.NET Core może przechowywać dane konfiguracyjne dla aplikacji w dowolnym pliku i ładować je w ramach uruchamiania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="fbf23-162">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="fbf23-163">Domyślny plik używany w szablonach projektu to *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="fbf23-163">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="fbf23-164">Załadowanie tego pliku do wystąpienia `IConfiguration` wewnątrz aplikacji odbywa się w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fbf23-164">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="fbf23-165">Aplikacja odczytuje z `Configuration` programu, aby pobrać ustawienia:</span><span class="sxs-lookup"><span data-stu-id="fbf23-165">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="fbf23-166">Istnieją rozszerzenia tego podejścia, aby proces był bardziej niezawodny, na przykład przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection) (di) do załadowania usługi z tymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="fbf23-166">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="fbf23-167">Podejście DI zapewnia zestaw obiektów konfiguracji o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="fbf23-167">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="fbf23-168">**Uwaga:** Aby uzyskać bardziej szczegółowe informacje na temat konfiguracji ASP.NET Core, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fbf23-168">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="fbf23-169">Natywny wtrysk zależności</span><span class="sxs-lookup"><span data-stu-id="fbf23-169">Native dependency injection</span></span>

<span data-ttu-id="fbf23-170">Ważnym celem tworzenia dużych, skalowalnych aplikacji jest swobodne sprzęganie składników i usług.</span><span class="sxs-lookup"><span data-stu-id="fbf23-170">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="fbf23-171">[Iniekcja zależności](xref:fundamentals/dependency-injection) jest popularną techniką do osiągnięcia tego celu i jest natywnym składnikiem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbf23-171">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="fbf23-172">W aplikacjach ASP.NET deweloperzy korzystają z biblioteki innej firmy w celu zaimplementowania iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="fbf23-172">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="fbf23-173">Jedną z takich bibliotek jest platforma [Unity](https://github.com/unitycontainer/unity), świadczona przez wzorce firmy Microsoft & praktyk.</span><span class="sxs-lookup"><span data-stu-id="fbf23-173">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="fbf23-174">Przykładem konfiguracji iniekcji zależności przy użyciu aparatu Unity jest `IDependencyResolver` implementacja, która zawija `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="fbf23-174">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="fbf23-175">Utwórz wystąpienie obiektu `UnityContainer`, zarejestruj swoją usługę i Ustaw program rozpoznawania `HttpConfiguration` zależności na nowe wystąpienie `UnityResolver` dla kontenera:</span><span class="sxs-lookup"><span data-stu-id="fbf23-175">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="fbf23-176">Wstrzyknięcie `IProductRepository` w razie konieczności:</span><span class="sxs-lookup"><span data-stu-id="fbf23-176">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="fbf23-177">Ponieważ iniekcja zależności jest częścią ASP.NET Core, można dodać usługę w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbf23-177">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="fbf23-178">Repozytorium można wstrzyknąć w dowolnym miejscu, podobnie jak w przypadku aparatu Unity.</span><span class="sxs-lookup"><span data-stu-id="fbf23-178">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="fbf23-179">Aby uzyskać więcej informacji na temat iniekcji zależności w <xref:fundamentals/dependency-injection>ASP.NET Core, zobacz.</span><span class="sxs-lookup"><span data-stu-id="fbf23-179">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="fbf23-180">Obsługa plików statycznych</span><span class="sxs-lookup"><span data-stu-id="fbf23-180">Serving static files</span></span>

<span data-ttu-id="fbf23-181">Ważną częścią programowania w sieci Web jest możliwość obsłużynia statycznych zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="fbf23-181">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="fbf23-182">Najczęstszymi przykładami plików statycznych są HTML, CSS, JavaScript i obrazy.</span><span class="sxs-lookup"><span data-stu-id="fbf23-182">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="fbf23-183">Te pliki muszą być zapisane w opublikowanej lokalizacji aplikacji (lub sieci CDN) i przywoływane, tak aby mogły zostać załadowane przez żądanie.</span><span class="sxs-lookup"><span data-stu-id="fbf23-183">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="fbf23-184">Ten proces został zmieniony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fbf23-184">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="fbf23-185">W ASP.NET pliki statyczne są przechowywane w różnych katalogach i przywoływane w widokach.</span><span class="sxs-lookup"><span data-stu-id="fbf23-185">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="fbf23-186">W ASP.NET Core pliki statyczne są przechowywane w "katalogu głównym sieci Web" (*&lt;&gt;/wwwroot zawartości*), chyba że zostały skonfigurowane inaczej.</span><span class="sxs-lookup"><span data-stu-id="fbf23-186">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="fbf23-187">Pliki są ładowane do potoku żądania przez wywołanie metody `UseStaticFiles` rozszerzenia z: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="fbf23-187">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="fbf23-188">**Uwaga:** Jeśli .NET Framework określania wartości docelowej, zainstaluj pakiet `Microsoft.AspNetCore.StaticFiles`NuGet.</span><span class="sxs-lookup"><span data-stu-id="fbf23-188">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="fbf23-189">Na przykład zasób obrazu w folderze *wwwroot/images* jest dostępny dla przeglądarki w lokalizacji takiej jak `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="fbf23-189">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="fbf23-190">**Uwaga:** Aby uzyskać bardziej szczegółowe informacje na temat obsługi plików statycznych w ASP.NET Core, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fbf23-190">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbf23-191">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="fbf23-191">Additional resources</span></span>

* [<span data-ttu-id="fbf23-192">Przenoszenie bibliotek do programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fbf23-192">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
