---
title: Skonfiguruj element dostosowujący dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975221"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="e7829-103">Skonfiguruj element dostosowujący dla ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e7829-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="e7829-104">Blazor WebAssembly wykonuje przycinanie [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby zmniejszyć rozmiar opublikowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="e7829-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="e7829-105">Domyślnie przycinanie odbywa się podczas publikowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e7829-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="e7829-106">Przycinanie może mieć szkodliwe skutki.</span><span class="sxs-lookup"><span data-stu-id="e7829-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="e7829-107">W aplikacjach, które używają odbicia, element dostosowujący często nie może określić typów wymaganych do odbicia w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="e7829-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="e7829-108">Aby przyciąć aplikacje używające odbicia, należy uzyskać informacje o typach wymaganych do odbicia w kodzie aplikacji oraz w pakietach lub strukturach, od których zależy aplikacja.</span><span class="sxs-lookup"><span data-stu-id="e7829-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="e7829-109">Element dostosowujący nie może również reagować na dynamiczne zachowanie aplikacji w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="e7829-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="e7829-110">Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, należy regularnie testować opublikowane dane wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="e7829-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="e7829-111">Aby skonfigurować element dostosowujący, zobacz artykuł [Opcje przycinania](/dotnet/core/deploying/trimming-options) w dokumentacji .NET podstawy, która zawiera wskazówki dotyczące następujących tematów:</span><span class="sxs-lookup"><span data-stu-id="e7829-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="e7829-112">Wyłącz przycinanie dla całej aplikacji z `<PublishTrimmed>` właściwością w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e7829-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="e7829-113">Kontroluj, jak agresywnie nieużywane IL jest odrzucane przez element dostosowujący.</span><span class="sxs-lookup"><span data-stu-id="e7829-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="e7829-114">Zatrzymaj element dostosowujący z przycinania określonych zestawów.</span><span class="sxs-lookup"><span data-stu-id="e7829-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="e7829-115">Zestawy "główne" do przycinania.</span><span class="sxs-lookup"><span data-stu-id="e7829-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="e7829-116">Ostrzeżenia powierzchni dla typów odbite przez ustawienie `<SuppressTrimAnalysisWarnings>` właściwości na `false` w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="e7829-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="e7829-117">Obcinanie symboli formantów i degugger.</span><span class="sxs-lookup"><span data-stu-id="e7829-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="e7829-118">Ustaw funkcje dostosowujące dla funkcji biblioteki struktury przycinania.</span><span class="sxs-lookup"><span data-stu-id="e7829-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7829-119">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="e7829-119">Additional resources</span></span>

* [<span data-ttu-id="e7829-120">Przycinanie samodzielnych wdrożeń i plików wykonywalnych</span><span class="sxs-lookup"><span data-stu-id="e7829-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
