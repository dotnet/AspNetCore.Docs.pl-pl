---
title: Migracja z ASP.NET do ASP.NET Core
author: isaac2004
description: Uzyskaj wskazówki dotyczące migracji istniejących aplikacji ASP.NET MVC lub Web API do ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 68a45dc50e00bead564500a12509b62a4a193ec4
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511095"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="32927-103">Migracja z ASP.NET do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32927-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="32927-104">Przez [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="32927-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="32927-105">Ten artykuł służy jako przewodnik do migracji ASP.NET aplikacji do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32927-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32927-106">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="32927-106">Prerequisites</span></span>

[<span data-ttu-id="32927-107">.NET Core SDK 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="32927-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="32927-108">Platformy docelowe</span><span class="sxs-lookup"><span data-stu-id="32927-108">Target frameworks</span></span>

<span data-ttu-id="32927-109">ASP.NET Core oferują deweloperom elastyczność kierowania .NET Core, .NET Framework lub obu.</span><span class="sxs-lookup"><span data-stu-id="32927-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="32927-110">Zobacz [Wybieranie między .NET Core i .NET Framework dla aplikacji serwera,](/dotnet/standard/choosing-core-framework-server) aby określić, która struktura docelowa jest najbardziej odpowiednia.</span><span class="sxs-lookup"><span data-stu-id="32927-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="32927-111">Podczas kierowania .NET Framework, projekty muszą odwoływać się do poszczególnych pakietów NuGet.</span><span class="sxs-lookup"><span data-stu-id="32927-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="32927-112">Targetowanie .NET Core pozwala wyeliminować wiele wyraźnych odniesień do pakietów, dzięki [ASP.NET Core metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="32927-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="32927-113">Zainstaluj `Microsoft.AspNetCore.App` metapakiet w swoim projekcie:</span><span class="sxs-lookup"><span data-stu-id="32927-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="32927-114">Gdy metapakiet jest używany, żadne pakiety, do których odwołuje się w metapakiet są wdrażane z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="32927-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="32927-115">Magazyn środowiska uruchomieniowego .NET Core zawiera te zasoby i są one wstępnie kompilowane w celu zwiększenia wydajności.</span><span class="sxs-lookup"><span data-stu-id="32927-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="32927-116">Aby uzyskać więcej informacji, zobacz [metapakiet Microsoft.AspNetCore.App dla ASP.NET Core.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="32927-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="32927-117">Różnice w strukturze projektu</span><span class="sxs-lookup"><span data-stu-id="32927-117">Project structure differences</span></span>

<span data-ttu-id="32927-118">Format pliku *csproj* został uproszczony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32927-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="32927-119">Oto kilka znaczących zmian:</span><span class="sxs-lookup"><span data-stu-id="32927-119">Some notable changes include:</span></span>

- <span data-ttu-id="32927-120">Jawne włączenie plików nie jest konieczne, aby były one uważane za część projektu.</span><span class="sxs-lookup"><span data-stu-id="32927-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="32927-121">Zmniejsza to ryzyko konfliktów scalania XML podczas pracy z dużymi zespołami.</span><span class="sxs-lookup"><span data-stu-id="32927-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="32927-122">Nie ma żadnych odwołań opartych na identyfikatorze GUID do innych projektów, co zwiększa czytelność plików.</span><span class="sxs-lookup"><span data-stu-id="32927-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="32927-123">Plik można edytować bez zwalniania go w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="32927-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Opcja menu kontekstowego programu CSPROJ w programie Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="32927-125">Wymiana pliku Global.asax</span><span class="sxs-lookup"><span data-stu-id="32927-125">Global.asax file replacement</span></span>

<span data-ttu-id="32927-126">ASP.NET Core wprowadziła nowy mechanizm uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32927-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="32927-127">Punktem wejścia dla aplikacji ASP.NET jest plik *Global.asax.*</span><span class="sxs-lookup"><span data-stu-id="32927-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="32927-128">Zadania, takie jak konfiguracja trasy i filtrowanie i rejestracje obszarów, są obsługiwane w pliku *Global.asax.*</span><span class="sxs-lookup"><span data-stu-id="32927-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="32927-129">Takie podejście łączy aplikację i serwer, na którym jest wdrożony w sposób, który zakłóca implementację.</span><span class="sxs-lookup"><span data-stu-id="32927-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="32927-130">W celu oddzielenia, [OWIN](https://owin.org/) został wprowadzony w celu zapewnienia czystszego sposobu korzystania z wielu struktur razem.</span><span class="sxs-lookup"><span data-stu-id="32927-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="32927-131">OWIN udostępnia potok, aby dodać tylko moduły potrzebne.</span><span class="sxs-lookup"><span data-stu-id="32927-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="32927-132">Środowisko hostingu przyjmuje funkcję [Uruchamianie,](xref:fundamentals/startup) aby skonfigurować usługi i potok żądań aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32927-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="32927-133">`Startup`rejestruje zestaw oprogramowania pośredniczącego z aplikacją.</span><span class="sxs-lookup"><span data-stu-id="32927-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="32927-134">Dla każdego żądania aplikacja wywołuje każdy ze składników oprogramowania pośredniczącego ze wskaźnikiem head połączonej listy do istniejącego zestawu programów obsługi.</span><span class="sxs-lookup"><span data-stu-id="32927-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="32927-135">Każdy składnik oprogramowania pośredniczącego można dodać jeden lub więcej programów obsługi do potoku obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="32927-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="32927-136">Jest to realizowane przez zwrócenie odwołania do programu obsługi, który jest nowy szef listy.</span><span class="sxs-lookup"><span data-stu-id="32927-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="32927-137">Każdy program obsługi jest odpowiedzialny za zapamiętywanie i wywoływanie następnego programu obsługi na liście.</span><span class="sxs-lookup"><span data-stu-id="32927-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="32927-138">W ASP.NET Core punktem wejścia do aplikacji `Startup`jest i nie masz już zależności od *global.asax*.</span><span class="sxs-lookup"><span data-stu-id="32927-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="32927-139">Korzystając z funkcji OWIN z programem .NET Framework, użyj następującej opcji jako potoku:</span><span class="sxs-lookup"><span data-stu-id="32927-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="32927-140">Spowoduje to skonfigurowanie tras domyślnych i domyślnie XmlSerialization za pomocą Json.</span><span class="sxs-lookup"><span data-stu-id="32927-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="32927-141">W razie potrzeby dodaj inne oprogramowanie pośredniczące do tego potoku (usługi ładowania, ustawienia konfiguracji, pliki statyczne itp.).</span><span class="sxs-lookup"><span data-stu-id="32927-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="32927-142">ASP.NET Core używa podobnego podejścia, ale nie polega na OWIN do obsługi wpisu.</span><span class="sxs-lookup"><span data-stu-id="32927-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="32927-143">Zamiast tego odbywa się to *Program.cs* `Main` za pomocą metody Program.cs (podobnej do aplikacji konsoli) i `Startup` jest tam ładowana.</span><span class="sxs-lookup"><span data-stu-id="32927-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="32927-144">`Startup`musi zawierać `Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="32927-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="32927-145">W `Configure`, dodaj niezbędne oprogramowanie pośredniczące do potoku.</span><span class="sxs-lookup"><span data-stu-id="32927-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="32927-146">W poniższym przykładzie (z domyślnego szablonu witryny sieci Web) metody rozszerzenia konfigurują potok z obsługą:</span><span class="sxs-lookup"><span data-stu-id="32927-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="32927-147">Strony błędów</span><span class="sxs-lookup"><span data-stu-id="32927-147">Error pages</span></span>
- <span data-ttu-id="32927-148">Ścisłe bezpieczeństwo transportu HTTP</span><span class="sxs-lookup"><span data-stu-id="32927-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="32927-149">Przekierowanie HTTP do protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="32927-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="32927-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="32927-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="32927-151">Host i aplikacja zostały oddzielone, co zapewnia elastyczność przenoszenia do innej platformy w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="32927-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="32927-152">Aby uzyskać bardziej szczegółowe odniesienie do ASP.NET core startup i middleware, zobacz [Uruchamianie w ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="32927-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="32927-153">Konfiguracje sklepu</span><span class="sxs-lookup"><span data-stu-id="32927-153">Store configurations</span></span>

<span data-ttu-id="32927-154">ASP.NET obsługuje przechowywanie ustawień.</span><span class="sxs-lookup"><span data-stu-id="32927-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="32927-155">Te ustawienie jest używane, na przykład do obsługi środowiska, w którym aplikacje zostały wdrożone.</span><span class="sxs-lookup"><span data-stu-id="32927-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="32927-156">Powszechną praktyką było przechowywanie wszystkich niestandardowych `<appSettings>` par klucz-wartość w sekcji pliku *Web.config:*</span><span class="sxs-lookup"><span data-stu-id="32927-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="32927-157">Aplikacje odczytywać `ConfigurationManager.AppSettings` te `System.Configuration` ustawienia przy użyciu kolekcji w obszarze nazw:</span><span class="sxs-lookup"><span data-stu-id="32927-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="32927-158">ASP.NET Core może przechowywać dane konfiguracyjne dla aplikacji w dowolnym pliku i ładować je jako część uruchamiania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="32927-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="32927-159">Domyślnym plikiem używanym w szablonach projektu jest *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="32927-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="32927-160">Ładowanie tego pliku do `IConfiguration` wystąpienia wewnątrz aplikacji odbywa się w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="32927-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="32927-161">Aplikacja odczytuje `Configuration` z aby uzyskać ustawienia:</span><span class="sxs-lookup"><span data-stu-id="32927-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="32927-162">Istnieją rozszerzenia tego podejścia, aby proces bardziej niezawodne, takie jak przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection) (DI) do ładowania usługi z tymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="32927-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="32927-163">Podejście DI zapewnia silnie typiwany zestaw obiektów konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="32927-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="32927-164">Aby uzyskać bardziej szczegółowe odniesienie do ASP.NET konfiguracji rdzenia, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="32927-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="32927-165">Wstrzyknięcie zależności natywnej</span><span class="sxs-lookup"><span data-stu-id="32927-165">Native dependency injection</span></span>

<span data-ttu-id="32927-166">Ważnym celem podczas tworzenia dużych, skalowalnych aplikacji jest luźne sprzężenie komponentów i usług.</span><span class="sxs-lookup"><span data-stu-id="32927-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="32927-167">[Wtrysk zależności](xref:fundamentals/dependency-injection) jest popularną techniką osiągnięcia tego celu i jest natywnym składnikiem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32927-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="32927-168">W ASP.NET aplikacji deweloperzy polegają na bibliotece innej firmy w celu zaimplementowania iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="32927-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="32927-169">Jedną z takich bibliotek jest [Unity](https://github.com/unitycontainer/unity), dostarczone przez Microsoft Patterns & Praktyki.</span><span class="sxs-lookup"><span data-stu-id="32927-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="32927-170">Przykład konfigurowania iniekcji zależności z `IDependencyResolver` Unity implementuje, że zawija: `UnityContainer`</span><span class="sxs-lookup"><span data-stu-id="32927-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="32927-171">Utwórz wystąpienie `UnityContainer`usługi , zarejestruj usługę i ustaw `HttpConfiguration` program rozpoznawania zależności `UnityResolver` na nowe wystąpienie kontenera:</span><span class="sxs-lookup"><span data-stu-id="32927-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="32927-172">Wstrzyknąć `IProductRepository` w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="32927-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="32927-173">Ponieważ iniekcja zależności jest częścią ASP.NET Core, można `ConfigureServices` dodać usługę w metodzie *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="32927-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="32927-174">Repozytorium można wstrzyknąć w dowolnym miejscu, tak jak miało to miejsce w przypadku Unity.</span><span class="sxs-lookup"><span data-stu-id="32927-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="32927-175">Aby uzyskać więcej informacji na temat iniekcji zależności, zobacz [Iniekcja zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="32927-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="32927-176">Obsługa plików statycznych</span><span class="sxs-lookup"><span data-stu-id="32927-176">Serve static files</span></span>

<span data-ttu-id="32927-177">Ważną częścią tworzenia stron internetowych jest możliwość obsługi statycznych zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="32927-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="32927-178">Najczęstszymi przykładami plików statycznych są HTML, CSS, Javascript i obrazy.</span><span class="sxs-lookup"><span data-stu-id="32927-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="32927-179">Pliki te muszą być zapisane w opublikowanej lokalizacji aplikacji (lub CDN) i odwołuje się, aby można je było załadować przez żądanie.</span><span class="sxs-lookup"><span data-stu-id="32927-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="32927-180">Ten proces zmienił się w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32927-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="32927-181">W ASP.NET pliki statyczne są przechowywane w różnych katalogach i odwołuje się w widokach.</span><span class="sxs-lookup"><span data-stu-id="32927-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="32927-182">W ASP.NET Core pliki statyczne są przechowywane w "katalogu głównym sieci Web"*&lt;(root&gt;treści /wwwroot),* chyba że skonfigurowano inaczej.</span><span class="sxs-lookup"><span data-stu-id="32927-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="32927-183">Pliki są ładowane do potoku `UseStaticFiles` żądań, wywołując metodę rozszerzenia z: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="32927-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="32927-184">Jeśli jest kierowana na platformę `Microsoft.AspNetCore.StaticFiles`.NET Framework, zainstaluj pakiet NuGet .</span><span class="sxs-lookup"><span data-stu-id="32927-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="32927-185">Na przykład zasób obrazu w folderze *wwwroot/images* jest dostępny `http://<app>/images/<imageFileName>`dla przeglądarki w lokalizacji, takiej jak .</span><span class="sxs-lookup"><span data-stu-id="32927-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="32927-186">Aby uzyskać bardziej szczegółowe odniesienie do wyświetlania plików statycznych w ASP.NET Core, zobacz [Pliki statyczne](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="32927-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="32927-187">Wielowartowiowe pliki cookie</span><span class="sxs-lookup"><span data-stu-id="32927-187">Multi-value cookies</span></span>

<span data-ttu-id="32927-188">[Pliki cookie o wielu wartościach](xref:System.Web.HttpCookie.Values) nie są obsługiwane w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="32927-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="32927-189">Utwórz jeden plik cookie na wartość.</span><span class="sxs-lookup"><span data-stu-id="32927-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="32927-190">Częściowa migracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="32927-190">Partial app migration</span></span>

<span data-ttu-id="32927-191">Jednym z podejść do częściowej migracji aplikacji jest utworzenie poddołty iIS i tylko przenieść niektóre trasy z ASP.NET 4.x do ASP.NET Core przy jednoczesnym zachowaniu struktury adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="32927-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="32927-192">Rozważmy na przykład strukturę adresu URL aplikacji z pliku *applicationHost.config:*</span><span class="sxs-lookup"><span data-stu-id="32927-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

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

<span data-ttu-id="32927-193">Struktura katalogów:</span><span class="sxs-lookup"><span data-stu-id="32927-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="32927-194">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="32927-194">Additional resources</span></span>

- [<span data-ttu-id="32927-195">Przenoszenie bibliotek do rdzenia NET</span><span class="sxs-lookup"><span data-stu-id="32927-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
