---
title: Migrowanie z ASP.NET do ASP.NET Core
author: isaac2004
description: Otrzymuj wskazówki dotyczące migrowania istniejących aplikacji ASP.NET MVC lub Web API do ASP.NET Core. Web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7961890becc8f4513e0750f28341c9d4cf94e7ad
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413340"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="acb62-103">Migrowanie z ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acb62-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="acb62-104">Autor [Tomasz Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="acb62-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="acb62-105">Ten artykuł służy jako Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb62-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span> <span data-ttu-id="acb62-106">Zapoznaj się z artykułem eksportowanie [istniejących aplikacji ASP.NET do platformy .NET Core](https://aka.ms/aspnet-porting-ebook) w celu uzyskania kompleksowego przewodnika dotyczącego przenoszenia.</span><span class="sxs-lookup"><span data-stu-id="acb62-106">See the ebook [Porting existing ASP.NET apps to .NET Core](https://aka.ms/aspnet-porting-ebook) for a comprehensive porting guide.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="acb62-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="acb62-107">Prerequisites</span></span>

[<span data-ttu-id="acb62-108">Zestaw .NET Core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="acb62-108">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="acb62-109">Platformy docelowe</span><span class="sxs-lookup"><span data-stu-id="acb62-109">Target frameworks</span></span>

<span data-ttu-id="acb62-110">Projekty ASP.NET Core oferują deweloperom elastyczność określania platformy .NET Core, .NET Framework lub obu.</span><span class="sxs-lookup"><span data-stu-id="acb62-110">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="acb62-111">Dowiedz się, [jak wybrać platformę .NET Core i .NET Framework dla aplikacji serwerowych](/dotnet/standard/choosing-core-framework-server) , aby określić, która platforma docelowa jest najbardziej odpowiednia.</span><span class="sxs-lookup"><span data-stu-id="acb62-111">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="acb62-112">W przypadku .NET Framework określania wartości docelowej projekty muszą odwoływać się do poszczególnych pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="acb62-112">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="acb62-113">Kierowanie programu .NET Core umożliwia eliminację wielu jawnych odwołań do pakietów, dzięki [czemu ASP.NET Core.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="acb62-113">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="acb62-114">Zainstaluj `Microsoft.AspNetCore.App` pakiet w projekcie:</span><span class="sxs-lookup"><span data-stu-id="acb62-114">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="acb62-115">W przypadku użycia pakietu z aplikacją nie są wdrażane żadne pakiety, do których odwołuje się pakiet.</span><span class="sxs-lookup"><span data-stu-id="acb62-115">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="acb62-116">Magazyn środowiska uruchomieniowego .NET Core zawiera te zasoby i są wstępnie skompilowane w celu zwiększenia wydajności.</span><span class="sxs-lookup"><span data-stu-id="acb62-116">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="acb62-117">Aby uzyskać więcej informacji, zobacz [Microsoft. AspNetCore. App Package for ASP.NET Core](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="acb62-117">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="acb62-118">Różnice struktury projektu</span><span class="sxs-lookup"><span data-stu-id="acb62-118">Project structure differences</span></span>

<span data-ttu-id="acb62-119">Format pliku *. csproj* został uproszczony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb62-119">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="acb62-120">Niektóre istotne zmiany obejmują:</span><span class="sxs-lookup"><span data-stu-id="acb62-120">Some notable changes include:</span></span>

- <span data-ttu-id="acb62-121">Jawne dołączenie plików nie jest konieczne, aby były uważane za część projektu.</span><span class="sxs-lookup"><span data-stu-id="acb62-121">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="acb62-122">Zmniejsza to ryzyko konfliktów scalania XML podczas pracy nad dużymi zespołami.</span><span class="sxs-lookup"><span data-stu-id="acb62-122">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="acb62-123">Nie ma żadnych odwołań opartych na identyfikatorach GUID do innych projektów, co zwiększa czytelność plików.</span><span class="sxs-lookup"><span data-stu-id="acb62-123">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="acb62-124">Plik można edytować bez zwalniania go w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="acb62-124">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Edytuj opcję menu kontekstowego CSPROJ w programie Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="acb62-126">Zastąpienie pliku Global. asax</span><span class="sxs-lookup"><span data-stu-id="acb62-126">Global.asax file replacement</span></span>

<span data-ttu-id="acb62-127">ASP.NET Core wprowadzono nowy mechanizm uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="acb62-127">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="acb62-128">Punkt wejścia dla aplikacji ASP.NET to plik *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="acb62-128">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="acb62-129">Zadania, takie jak konfiguracja tras oraz filtrowanie i rejestrowanie obszaru, są obsługiwane w pliku *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="acb62-129">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="acb62-130">Takie podejście Couples aplikację i serwer, na który jest wdrażana w sposób, który zakłóca implementację.</span><span class="sxs-lookup"><span data-stu-id="acb62-130">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="acb62-131">W celu oddzielenia [Owin](https://owin.org/) został wprowadzony w celu zapewnienia bardziej przejrzystego sposobu używania wielu struktur.</span><span class="sxs-lookup"><span data-stu-id="acb62-131">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="acb62-132">OWIN zapewnia potok do dodawania tylko wymaganych modułów.</span><span class="sxs-lookup"><span data-stu-id="acb62-132">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="acb62-133">Środowisko hostingu wykonuje funkcję [uruchamiania](xref:fundamentals/startup) , aby skonfigurować usługi i potok żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="acb62-133">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="acb62-134">`Startup` rejestruje zestaw programów pośredniczących w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="acb62-134">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="acb62-135">Dla każdego żądania aplikacja wywołuje każdy składnik pośredniczący ze wskaźnikiem głównym połączonej listy z istniejącym zestawem programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="acb62-135">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="acb62-136">Każdy składnik pośredniczący może dodać jeden lub więcej programów obsługi do potoku obsługi żądania.</span><span class="sxs-lookup"><span data-stu-id="acb62-136">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="acb62-137">Jest to realizowane przez zwrócenie odwołania do programu obsługi, który jest nowym szefem listy.</span><span class="sxs-lookup"><span data-stu-id="acb62-137">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="acb62-138">Każdy program obsługi jest odpowiedzialny za zapamiętywanie i wywoływanie kolejnej procedury obsługi na liście.</span><span class="sxs-lookup"><span data-stu-id="acb62-138">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="acb62-139">W przypadku ASP.NET Core punkt wejścia do aplikacji jest `Startup` i nie ma już zależności od elementu *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="acb62-139">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="acb62-140">W przypadku korzystania z programu OWIN z .NET Framework należy użyć podobnej do poniższej postaci potoku:</span><span class="sxs-lookup"><span data-stu-id="acb62-140">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="acb62-141">Powoduje to skonfigurowanie tras domyślnych i wartości domyślnych XmlSerialization w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="acb62-141">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="acb62-142">W razie potrzeby Dodaj inne oprogramowanie pośredniczące (ładowanie usług, ustawień konfiguracji, plików statycznych itp.).</span><span class="sxs-lookup"><span data-stu-id="acb62-142">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="acb62-143">ASP.NET Core używa podobnego podejścia, ale nie polega na OWIN do obsługi wpisu.</span><span class="sxs-lookup"><span data-stu-id="acb62-143">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="acb62-144">Zamiast tego robi to za pomocą metody *program.cs* `Main` (podobnie jak aplikacje konsolowe) i `Startup` jest ładowany w tym miejscu.</span><span class="sxs-lookup"><span data-stu-id="acb62-144">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="acb62-145">`Startup` musi zawierać `Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="acb62-145">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="acb62-146">W programie `Configure` Dodaj wymagane oprogramowanie pośredniczące do potoku.</span><span class="sxs-lookup"><span data-stu-id="acb62-146">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="acb62-147">W poniższym przykładzie (z domyślnego szablonu witryny sieci Web) metody rozszerzenia konfigurują potok z obsługą:</span><span class="sxs-lookup"><span data-stu-id="acb62-147">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="acb62-148">Strony błędów</span><span class="sxs-lookup"><span data-stu-id="acb62-148">Error pages</span></span>
- <span data-ttu-id="acb62-149">Zabezpieczenia protokołu HTTP Strict Transport</span><span class="sxs-lookup"><span data-stu-id="acb62-149">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="acb62-150">Przekierowywanie HTTP do protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="acb62-150">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="acb62-151">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="acb62-151">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="acb62-152">Host i aplikacja zostały odłączone, co zapewnia elastyczność przejścia do innej platformy w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="acb62-152">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="acb62-153">Aby uzyskać bardziej szczegółowe informacje dotyczące ASP.NET Core uruchamiania i oprogramowania pośredniczącego, zobacz [Uruchamianie w ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="acb62-153">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="acb62-154">Konfiguracje magazynu</span><span class="sxs-lookup"><span data-stu-id="acb62-154">Store configurations</span></span>

<span data-ttu-id="acb62-155">ASP.NET obsługuje przechowywanie ustawień.</span><span class="sxs-lookup"><span data-stu-id="acb62-155">ASP.NET supports storing settings.</span></span> <span data-ttu-id="acb62-156">Te ustawienia są używane na przykład w celu obsługi środowiska, w którym aplikacje zostały wdrożone.</span><span class="sxs-lookup"><span data-stu-id="acb62-156">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="acb62-157">Typowym celem jest przechowywanie wszystkich niestandardowych par klucz-wartość w `<appSettings>` sekcji pliku *Web.config* :</span><span class="sxs-lookup"><span data-stu-id="acb62-157">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="acb62-158">Aplikacje odczytują te ustawienia przy użyciu `ConfigurationManager.AppSettings` kolekcji w `System.Configuration` przestrzeni nazw:</span><span class="sxs-lookup"><span data-stu-id="acb62-158">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="acb62-159">ASP.NET Core może przechowywać dane konfiguracyjne dla aplikacji w dowolnym pliku i ładować je w ramach uruchamiania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="acb62-159">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="acb62-160">Domyślny plik używany w szablonach projektu to *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="acb62-160">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="acb62-161">Załadowanie tego pliku do wystąpienia `IConfiguration` wewnątrz aplikacji odbywa się w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="acb62-161">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="acb62-162">Aplikacja odczytuje z `Configuration` programu, aby pobrać ustawienia:</span><span class="sxs-lookup"><span data-stu-id="acb62-162">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="acb62-163">Istnieją rozszerzenia tego podejścia, aby proces był bardziej niezawodny, na przykład przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection) (di) do załadowania usługi z tymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="acb62-163">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="acb62-164">Podejście DI zapewnia zestaw obiektów konfiguracji o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="acb62-164">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="acb62-165">Aby uzyskać bardziej szczegółowe informacje na temat konfiguracji ASP.NET Core, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="acb62-165">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="acb62-166">Natywny wtrysk zależności</span><span class="sxs-lookup"><span data-stu-id="acb62-166">Native dependency injection</span></span>

<span data-ttu-id="acb62-167">Ważnym celem tworzenia dużych, skalowalnych aplikacji jest swobodne sprzęganie składników i usług.</span><span class="sxs-lookup"><span data-stu-id="acb62-167">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="acb62-168">[Iniekcja zależności](xref:fundamentals/dependency-injection) jest popularną techniką do osiągnięcia tego celu i jest natywnym składnikiem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb62-168">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="acb62-169">W aplikacjach ASP.NET deweloperzy korzystają z biblioteki innej firmy w celu zaimplementowania iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="acb62-169">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="acb62-170">Jedną z takich bibliotek jest platforma [Unity](https://github.com/unitycontainer/unity), świadczona przez wzorce firmy Microsoft & praktyk.</span><span class="sxs-lookup"><span data-stu-id="acb62-170">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="acb62-171">Przykładem konfiguracji iniekcji zależności przy użyciu aparatu Unity jest implementacja `IDependencyResolver` , która zawija `UnityContainer` :</span><span class="sxs-lookup"><span data-stu-id="acb62-171">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="acb62-172">Utwórz wystąpienie obiektu `UnityContainer` , zarejestruj swoją usługę i Ustaw program rozpoznawania zależności na `HttpConfiguration` nowe wystąpienie `UnityResolver` dla kontenera:</span><span class="sxs-lookup"><span data-stu-id="acb62-172">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="acb62-173">Wstrzyknięcie w `IProductRepository` razie konieczności:</span><span class="sxs-lookup"><span data-stu-id="acb62-173">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="acb62-174">Ponieważ iniekcja zależności jest częścią ASP.NET Core, możesz dodać swoją usługę w `ConfigureServices` metodzie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="acb62-174">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="acb62-175">Repozytorium można wstrzyknąć w dowolnym miejscu, podobnie jak w przypadku aparatu Unity.</span><span class="sxs-lookup"><span data-stu-id="acb62-175">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="acb62-176">Aby uzyskać więcej informacji na temat iniekcji zależności, zobacz [iniekcja zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="acb62-176">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="acb62-177">Obsługuj pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="acb62-177">Serve static files</span></span>

<span data-ttu-id="acb62-178">Ważną częścią programowania w sieci Web jest możliwość obsłużynia statycznych zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="acb62-178">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="acb62-179">Najczęstszymi przykładami plików statycznych są HTML, CSS, JavaScript i obrazy.</span><span class="sxs-lookup"><span data-stu-id="acb62-179">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="acb62-180">Te pliki muszą być zapisane w opublikowanej lokalizacji aplikacji (lub sieci CDN) i przywoływane, tak aby mogły zostać załadowane przez żądanie.</span><span class="sxs-lookup"><span data-stu-id="acb62-180">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="acb62-181">Ten proces został zmieniony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb62-181">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="acb62-182">W ASP.NET pliki statyczne są przechowywane w różnych katalogach i przywoływane w widokach.</span><span class="sxs-lookup"><span data-stu-id="acb62-182">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="acb62-183">W ASP.NET Core pliki statyczne są przechowywane w "katalogu głównym sieci Web" (*&lt; &gt; /wwwroot zawartości*), chyba że zostały skonfigurowane inaczej.</span><span class="sxs-lookup"><span data-stu-id="acb62-183">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="acb62-184">Pliki są ładowane do potoku żądania przez wywołanie `UseStaticFiles` metody rozszerzenia z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="acb62-184">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="acb62-185">Jeśli .NET Framework określania wartości docelowej, zainstaluj pakiet NuGet `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="acb62-185">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="acb62-186">Na przykład zasób obrazu w folderze *wwwroot/images* jest dostępny dla przeglądarki w lokalizacji takiej jak `http://<app>/images/<imageFileName>` .</span><span class="sxs-lookup"><span data-stu-id="acb62-186">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="acb62-187">Aby uzyskać bardziej szczegółowe informacje na temat obsługi plików statycznych w ASP.NET Core, zobacz [pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="acb62-187">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="acb62-188">Wiele wartości cookie s</span><span class="sxs-lookup"><span data-stu-id="acb62-188">Multi-value cookies</span></span>

<span data-ttu-id="acb62-189">W ASP.NET Core nie są obsługiwane [wiele wartości cookie s](xref:System.Web.HttpCookie.Values) .</span><span class="sxs-lookup"><span data-stu-id="acb62-189">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="acb62-190">Utwórz jedną cookie na wartość.</span><span class="sxs-lookup"><span data-stu-id="acb62-190">Create one cookie per value.</span></span>

## <a name="authentication-cookies-are-not-compressed-in-aspnet-core"></a><span data-ttu-id="acb62-191">Uwierzytelnianie cookie s nie jest kompresowane w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="acb62-191">Authentication cookies are not compressed in ASP.NET Core</span></span>

[!INCLUDE[](~/includes/cookies-not-compressed.md)]

## <a name="partial-app-migration"></a><span data-ttu-id="acb62-192">Migracja częściowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="acb62-192">Partial app migration</span></span>

<span data-ttu-id="acb62-193">Jednym z metod migracji części aplikacji jest utworzenie aplikacji podrzędnej IIS i przeniesienie niektórych tras z ASP.NET 4. x do ASP.NET Core przy zachowaniu struktury adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="acb62-193">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="acb62-194">Rozważmy na przykład strukturę adresu URL aplikacji z pliku *applicationHost.config* :</span><span class="sxs-lookup"><span data-stu-id="acb62-194">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="acb62-195">Struktura katalogów:</span><span class="sxs-lookup"><span data-stu-id="acb62-195">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a><span data-ttu-id="acb62-196">[BIND] i wejściowe elementy formatującego</span><span class="sxs-lookup"><span data-stu-id="acb62-196">[BIND] and Input Formatters</span></span>

<span data-ttu-id="acb62-197">[Poprzednie wersje ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) używały `[Bind]` atrybutu do ochrony przed atakami polegającymi na przeniesieniu.</span><span class="sxs-lookup"><span data-stu-id="acb62-197">[Previous versions of ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) used the `[Bind]` attribute to protect against overposting attacks.</span></span> <span data-ttu-id="acb62-198">[Wejściowe elementy formatującego](xref:mvc/models/model-binding#input-formatters) działają inaczej w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="acb62-198">[Input formatters](xref:mvc/models/model-binding#input-formatters) work differently in ASP.NET Core.</span></span> <span data-ttu-id="acb62-199">Ten `[Bind]` atrybut nie jest już zaprojektowany tak, aby zapobiec nadpisywaniu, gdy jest używany z danymi wejściowymi w celu przeanalizowania JSON lub XML.</span><span class="sxs-lookup"><span data-stu-id="acb62-199">The `[Bind]` attribute is no longer designed to prevent overposting when used with input formatters to parse JSON or XML.</span></span> <span data-ttu-id="acb62-200">Te atrybuty wpływają na powiązanie modelu, gdy źródło danych jest formularzem danych publikowanych z `x-www-form-urlencoded` typem zawartości.</span><span class="sxs-lookup"><span data-stu-id="acb62-200">These attributes affect model binding when the source of data is form data posted with the `x-www-form-urlencoded` content type.</span></span>

<span data-ttu-id="acb62-201">W przypadku aplikacji, które wysyłają informacje JSON do kontrolerów i używają programu formatującego dane wejściowe JSON w celu przeanalizowania danych, zalecamy zamianę `[Bind]` atrybutu z modelem widoku, który odpowiada właściwościom zdefiniowanym przez `[Bind]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="acb62-201">For apps that post JSON information to controllers and use JSON Input Formatters to parse the data, we recommend replacing the `[Bind]` attribute with a view model that matches the properties defined by the `[Bind]` attribute.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="acb62-202">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="acb62-202">Additional resources</span></span>

- [<span data-ttu-id="acb62-203">Przenoszenie bibliotek do programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="acb62-203">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
