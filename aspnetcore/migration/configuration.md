---
title: Migrowanie konfiguracji do ASP.NET Core
author: ardalis
description: Dowiedz się, jak przeprowadzić migrację konfiguracji z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: 9be321850b14847973877fb6a32217bd2dbb5171
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399819"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="c45ab-103">Migrowanie konfiguracji do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c45ab-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="c45ab-104">[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="c45ab-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="c45ab-105">W poprzednim artykule rozpocząłmy [migrację projektu ASP.NET MVC do ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="c45ab-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="c45ab-106">W tym artykule Migrujemy konfigurację.</span><span class="sxs-lookup"><span data-stu-id="c45ab-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="c45ab-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c45ab-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="c45ab-108">Konfiguracja konfiguracji</span><span class="sxs-lookup"><span data-stu-id="c45ab-108">Setup configuration</span></span>

<span data-ttu-id="c45ab-109">ASP.NET Core nie używa już plików *Global. asax* i *web.config* , które zostały wykorzystane przez poprzednie wersje ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="c45ab-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="c45ab-110">We wcześniejszych wersjach programu ASP.NET logika uruchamiania aplikacji została umieszczona w `Application_StartUp` metodzie w *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="c45ab-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="c45ab-111">Później w ASP.NET MVC plik *Startup.cs* został uwzględniony w katalogu głównym projektu; i, został wywołany podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c45ab-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="c45ab-112">ASP.NET Core częściowo przyjęła to podejście, umieszczając w pliku *Startup.cs* wszystkie logikę uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="c45ab-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="c45ab-113">Plik *web.config* został również zastąpiony przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c45ab-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="c45ab-114">Konfigurację można teraz skonfigurować w ramach procedury uruchamiania aplikacji opisanej w *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="c45ab-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="c45ab-115">Konfiguracja może nadal korzystać z plików XML, ale zazwyczaj projekty ASP.NET Core umieściją wartości konfiguracyjne w pliku w formacie JSON, takim jak *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c45ab-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="c45ab-116">System konfiguracji ASP.NET Core może również łatwo uzyskać dostęp do zmiennych środowiskowych, co może zapewnić bezpieczniejsze [i niezawodne lokalizację](xref:security/app-secrets) dla wartości specyficznych dla środowiska.</span><span class="sxs-lookup"><span data-stu-id="c45ab-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="c45ab-117">Jest to szczególnie prawdziwe w przypadku wpisów tajnych, takich jak parametry połączenia i klucze interfejsu API, które nie powinny być zaewidencjonowane do kontroli źródła.</span><span class="sxs-lookup"><span data-stu-id="c45ab-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="c45ab-118">Zobacz [Konfiguracja](xref:fundamentals/configuration/index) , aby dowiedzieć się więcej o konfiguracji w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c45ab-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="c45ab-119">W tym artykule zaczynamy od częściowo zmigrowanego projektu ASP.NET Core z [poprzedniego artykułu](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="c45ab-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="c45ab-120">Aby skonfigurować konfigurację, Dodaj następujący Konstruktor i właściwość do pliku *Startup.cs* znajdującego się w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="c45ab-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="c45ab-121">Należy pamiętać, że w tym momencie plik *Startup.cs* nie zostanie skompilowany, ponieważ nadal będziemy musieli dodać następującą `using` instrukcję:</span><span class="sxs-lookup"><span data-stu-id="c45ab-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="c45ab-122">Dodaj *appsettings.jsw* pliku do katalogu głównego projektu przy użyciu odpowiedniego szablonu elementu:</span><span class="sxs-lookup"><span data-stu-id="c45ab-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Dodawanie pliku JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="c45ab-124">Migrowanie ustawień konfiguracji z web.config</span><span class="sxs-lookup"><span data-stu-id="c45ab-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="c45ab-125">Nasze projekty MVC ASP.NET zawierają wymagane parametry połączenia z bazą danych w *web.config*w `<connectionStrings>` elemencie.</span><span class="sxs-lookup"><span data-stu-id="c45ab-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="c45ab-126">W naszym ASP.NET Core projekcie będziemy przechowywać te informacje w *appsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="c45ab-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="c45ab-127">Otwórz *appsettings.jsna*i zwróć uwagę, że zawiera on już następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="c45ab-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="c45ab-128">W wyróżnionym wierszu poniżej Zmień nazwę bazy danych z **_CHANGE_ME** na nazwę bazy danych.</span><span class="sxs-lookup"><span data-stu-id="c45ab-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="c45ab-129">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="c45ab-129">Summary</span></span>

<span data-ttu-id="c45ab-130">ASP.NET Core umieszcza wszystkie logikę uruchamiania aplikacji w pojedynczym pliku, w którym można zdefiniować i skonfigurować wymagane usługi i zależności.</span><span class="sxs-lookup"><span data-stu-id="c45ab-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="c45ab-131">Zastępuje plik *web.config* elastyczną funkcją konfiguracji, która może korzystać z różnych formatów plików, takich jak JSON, a także zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="c45ab-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
