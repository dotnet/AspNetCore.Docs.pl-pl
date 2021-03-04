---
title: Przykłady uwierzytelniania dla ASP.NET Core
author: rick-anderson
description: Zawiera łącza do przykładów uwierzytelniania w repozytorium ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110121"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="76ae3-103">Przykłady uwierzytelniania dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76ae3-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="76ae3-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76ae3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="76ae3-105">[Repozytorium ASP.NET Core](https://github.com/dotnet/aspnetcore) zawiera następujące przykłady uwierzytelniania ( `main` gałąź):</span><span class="sxs-lookup"><span data-stu-id="76ae3-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="76ae3-106">Przekształcanie oświadczeń</span><span class="sxs-lookup"><span data-stu-id="76ae3-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="76ae3-107">[Cookie ponowne](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="76ae3-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="76ae3-108">Niestandardowa odpowiedź na niepowodzenie autoryzacji</span><span class="sxs-lookup"><span data-stu-id="76ae3-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="76ae3-109">Niestandardowy dostawca zasad — IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="76ae3-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="76ae3-110">Dynamiczne schematy i opcje uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="76ae3-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="76ae3-111">[Oświadczenia zewnętrzne](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="76ae3-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="76ae3-112">Wybieranie między cookie i innym schematem uwierzytelniania opartym na żądaniu</span><span class="sxs-lookup"><span data-stu-id="76ae3-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="76ae3-113">Ogranicza dostęp do plików statycznych</span><span class="sxs-lookup"><span data-stu-id="76ae3-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="76ae3-114">Pobierz i uruchom przykłady</span><span class="sxs-lookup"><span data-stu-id="76ae3-114">Obtain and run the samples</span></span>

<span data-ttu-id="76ae3-115">Przykładowe linki podane w tym artykule zawierają przykłady dla nadchodzącej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="76ae3-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="76ae3-116">Aby uzyskać przykład dla bieżącej wersji lub wcześniejszej wersji, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="76ae3-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="76ae3-117">Wybierz gałąź wydania [repozytorium ASP.NET Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) .</span><span class="sxs-lookup"><span data-stu-id="76ae3-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="76ae3-118">Na przykład `release/5.0` gałąź zawiera przykłady dla wydania ASP.NET Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="76ae3-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="76ae3-119">Klonuj lub Pobierz repozytorium ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="76ae3-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="76ae3-120">Sprawdź, czy w systemie lokalnym jest zainstalowany [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) wersja zgodna z klonem ASP.NET Core repozytorium.</span><span class="sxs-lookup"><span data-stu-id="76ae3-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="76ae3-121">Przejdź do przykładowego `aspnetcore/src/Security/samples` folderu w folderze, a następnie uruchom próbkę za pomocą [ `dotnet run` polecenia](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="76ae3-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
