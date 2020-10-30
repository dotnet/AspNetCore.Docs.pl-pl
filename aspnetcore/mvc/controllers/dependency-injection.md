---
title: Wstrzykiwanie zależności do kontrolerów w ASP.NET Core
author: ardalis
description: Odkryj, jak ASP.NET Core kontrolery MVC żądają swoich zależności jawnie za pośrednictwem konstruktorów z iniekcją zależności w ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 1282cd984584be423fba755e64e5d2f1afd2af89
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060615"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="75c39-103">Wstrzykiwanie zależności do kontrolerów w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75c39-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="75c39-104">Autorzy [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="75c39-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="75c39-105">ASP.NET Core kontrolery MVC żądają zależności jawnie za pośrednictwem konstruktorów.</span><span class="sxs-lookup"><span data-stu-id="75c39-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="75c39-106">ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75c39-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75c39-107">Dzięki temu aplikacje są łatwiejsze do testowania i konserwowania.</span><span class="sxs-lookup"><span data-stu-id="75c39-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="75c39-108">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75c39-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="75c39-109">Iniekcja konstruktora</span><span class="sxs-lookup"><span data-stu-id="75c39-109">Constructor Injection</span></span>

<span data-ttu-id="75c39-110">Usługi są dodawane jako parametr konstruktora, a środowisko uruchomieniowe rozwiązuje usługę z kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="75c39-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="75c39-111">Usługi są zwykle definiowane przy użyciu interfejsów.</span><span class="sxs-lookup"><span data-stu-id="75c39-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="75c39-112">Rozważmy na przykład aplikację, która wymaga bieżącego czasu.</span><span class="sxs-lookup"><span data-stu-id="75c39-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="75c39-113">Następujący interfejs uwidacznia `IDateTime` usługę:</span><span class="sxs-lookup"><span data-stu-id="75c39-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="75c39-114">Poniższy kod implementuje `IDateTime` Interfejs:</span><span class="sxs-lookup"><span data-stu-id="75c39-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="75c39-115">Dodaj usługę do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="75c39-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="75c39-116">Aby uzyskać więcej informacji na temat <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> , zobacz [di okresy istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="75c39-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="75c39-117">Poniższy kod wyświetla powitanie użytkownika w oparciu o godzinę:</span><span class="sxs-lookup"><span data-stu-id="75c39-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="75c39-118">Uruchom aplikację i zostanie wyświetlony komunikat z uwzględnieniem czasu.</span><span class="sxs-lookup"><span data-stu-id="75c39-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="75c39-119">Iniekcja akcji przy użyciu FromServices</span><span class="sxs-lookup"><span data-stu-id="75c39-119">Action injection with FromServices</span></span>

<span data-ttu-id="75c39-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>Umożliwia wstrzyknięcie usługi bezpośrednio do metody akcji bez użycia iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="75c39-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="75c39-121">Ustawienia dostępu z kontrolera</span><span class="sxs-lookup"><span data-stu-id="75c39-121">Access settings from a controller</span></span>

<span data-ttu-id="75c39-122">Dostęp do ustawień aplikacji lub konfiguracji z poziomu kontrolera jest typowym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="75c39-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="75c39-123">*Wzorzec opcji* opisany w <xref:fundamentals/configuration/options> jest preferowanym podejściem do zarządzania ustawieniami.</span><span class="sxs-lookup"><span data-stu-id="75c39-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="75c39-124">Ogólnie rzecz biorąc nie należy wprowadzać bezpośrednio <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="75c39-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="75c39-125">Utwórz klasę, która reprezentuje opcje.</span><span class="sxs-lookup"><span data-stu-id="75c39-125">Create a class that represents the options.</span></span> <span data-ttu-id="75c39-126">Przykład:</span><span class="sxs-lookup"><span data-stu-id="75c39-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="75c39-127">Dodaj klasę konfiguracji do kolekcji usług:</span><span class="sxs-lookup"><span data-stu-id="75c39-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="75c39-128">Skonfiguruj aplikację do odczytywania ustawień z pliku w formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="75c39-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="75c39-129">Poniższy kod żąda `IOptions<SampleWebSettings>` ustawień z kontenera usługi i używa ich w `Index` metodzie:</span><span class="sxs-lookup"><span data-stu-id="75c39-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="75c39-130">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="75c39-130">Additional resources</span></span>

* <span data-ttu-id="75c39-131">Zobacz <xref:mvc/controllers/testing> , aby dowiedzieć się, jak ułatwić przetestowanie kodu przez jawne żądanie zależności w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="75c39-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="75c39-132">[Zastąp domyślny kontener iniekcji zależności z implementacją innej firmy](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="75c39-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="75c39-133">Autorzy [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)i [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="75c39-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="75c39-134">ASP.NET Core kontrolery MVC żądają zależności jawnie za pośrednictwem konstruktorów.</span><span class="sxs-lookup"><span data-stu-id="75c39-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="75c39-135">ASP.NET Core ma wbudowaną obsługę [iniekcji zależności (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="75c39-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="75c39-136">Dzięki temu aplikacje są łatwiejsze do testowania i konserwowania.</span><span class="sxs-lookup"><span data-stu-id="75c39-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="75c39-137">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75c39-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="75c39-138">Iniekcja konstruktora</span><span class="sxs-lookup"><span data-stu-id="75c39-138">Constructor Injection</span></span>

<span data-ttu-id="75c39-139">Usługi są dodawane jako parametr konstruktora, a środowisko uruchomieniowe rozwiązuje usługę z kontenera usług.</span><span class="sxs-lookup"><span data-stu-id="75c39-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="75c39-140">Usługi są zwykle definiowane przy użyciu interfejsów.</span><span class="sxs-lookup"><span data-stu-id="75c39-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="75c39-141">Rozważmy na przykład aplikację, która wymaga bieżącego czasu.</span><span class="sxs-lookup"><span data-stu-id="75c39-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="75c39-142">Następujący interfejs uwidacznia `IDateTime` usługę:</span><span class="sxs-lookup"><span data-stu-id="75c39-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="75c39-143">Poniższy kod implementuje `IDateTime` Interfejs:</span><span class="sxs-lookup"><span data-stu-id="75c39-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="75c39-144">Dodaj usługę do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="75c39-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="75c39-145">Aby uzyskać więcej informacji na temat <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> , zobacz [di okresy istnienia usługi](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="75c39-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="75c39-146">Poniższy kod wyświetla powitanie użytkownika w oparciu o godzinę:</span><span class="sxs-lookup"><span data-stu-id="75c39-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="75c39-147">Uruchom aplikację i zostanie wyświetlony komunikat z uwzględnieniem czasu.</span><span class="sxs-lookup"><span data-stu-id="75c39-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="75c39-148">Iniekcja akcji przy użyciu FromServices</span><span class="sxs-lookup"><span data-stu-id="75c39-148">Action injection with FromServices</span></span>

<span data-ttu-id="75c39-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>Umożliwia wstrzyknięcie usługi bezpośrednio do metody akcji bez użycia iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="75c39-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="75c39-150">Ustawienia dostępu z kontrolera</span><span class="sxs-lookup"><span data-stu-id="75c39-150">Access settings from a controller</span></span>

<span data-ttu-id="75c39-151">Dostęp do ustawień aplikacji lub konfiguracji z poziomu kontrolera jest typowym wzorcem.</span><span class="sxs-lookup"><span data-stu-id="75c39-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="75c39-152">*Wzorzec opcji* opisany w <xref:fundamentals/configuration/options> jest preferowanym podejściem do zarządzania ustawieniami.</span><span class="sxs-lookup"><span data-stu-id="75c39-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="75c39-153">Ogólnie rzecz biorąc nie należy wprowadzać bezpośrednio <xref:Microsoft.Extensions.Configuration.IConfiguration> do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="75c39-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="75c39-154">Utwórz klasę, która reprezentuje opcje.</span><span class="sxs-lookup"><span data-stu-id="75c39-154">Create a class that represents the options.</span></span> <span data-ttu-id="75c39-155">Przykład:</span><span class="sxs-lookup"><span data-stu-id="75c39-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="75c39-156">Dodaj klasę konfiguracji do kolekcji usług:</span><span class="sxs-lookup"><span data-stu-id="75c39-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="75c39-157">Skonfiguruj aplikację do odczytywania ustawień z pliku w formacie JSON:</span><span class="sxs-lookup"><span data-stu-id="75c39-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="75c39-158">Poniższy kod żąda `IOptions<SampleWebSettings>` ustawień z kontenera usługi i używa ich w `Index` metodzie:</span><span class="sxs-lookup"><span data-stu-id="75c39-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="75c39-159">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="75c39-159">Additional resources</span></span>

* <span data-ttu-id="75c39-160">Zobacz <xref:mvc/controllers/testing> , aby dowiedzieć się, jak ułatwić przetestowanie kodu przez jawne żądanie zależności w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="75c39-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="75c39-161">[Zastąp domyślny kontener iniekcji zależności z implementacją innej firmy](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="75c39-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end