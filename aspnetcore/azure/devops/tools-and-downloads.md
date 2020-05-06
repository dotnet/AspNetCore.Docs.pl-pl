---
title: Narzędzia i pliki do pobrania — DevOps z ASP.NET Core i Azure
author: CamSoper
description: Narzędzia i materiały do pobrania wymagane do DevOps z usługami ASP.NET Core i Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 1917a329a5dcbe60542541cfcdc746799307e3d5
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850412"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="b833c-103">Narzędzia i pliki do pobrania</span><span class="sxs-lookup"><span data-stu-id="b833c-103">Tools and downloads</span></span>

<span data-ttu-id="b833c-104">Platforma Azure ma kilka interfejsów do aprowizacji zasobów i zarządzania nimi, takich jak [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)i programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b833c-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="b833c-105">Ten przewodnik przyjmuje podejście minimalistyczny i używa Azure Cloud Shell, gdy jest to możliwe, aby zmniejszyć wymaganą procedurę.</span><span class="sxs-lookup"><span data-stu-id="b833c-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="b833c-106">Jednak Azure Portal muszą być używane w niektórych częściach.</span><span class="sxs-lookup"><span data-stu-id="b833c-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b833c-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="b833c-107">Prerequisites</span></span>

<span data-ttu-id="b833c-108">Wymagane są następujące subskrypcje:</span><span class="sxs-lookup"><span data-stu-id="b833c-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="b833c-109">&mdash; Jeśli nie masz konta, możesz [skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="b833c-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="b833c-110">Azure DevOps Services &mdash; subskrypcję platformy Azure DevOps i organizacja jest tworzona w rozdziale 4.</span><span class="sxs-lookup"><span data-stu-id="b833c-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="b833c-111">GitHub &mdash; , jeśli nie masz konta, [zarejestruj się bezpłatnie](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="b833c-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="b833c-112">Wymagane są następujące narzędzia:</span><span class="sxs-lookup"><span data-stu-id="b833c-112">The following tools are required:</span></span>

* <span data-ttu-id="b833c-113">W tym przewodniku zalecamy podstawowe zrozumienie [usługi git.](https://git-scm.com/downloads) &mdash;</span><span class="sxs-lookup"><span data-stu-id="b833c-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="b833c-114">Przejrzyj [dokumentację usługi git](https://git-scm.com/doc), w tym [narzędzia Git Remote](https://git-scm.com/docs/git-remote) i [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="b833c-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="b833c-115">Do kompilowania i uruchamiania przykładowej aplikacji wymagany jest [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; w wersji 2.1.300 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="b833c-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="b833c-116">Jeśli program Visual Studio jest zainstalowany z obciążeniem **programistycznym dla wielu platform .NET Core** , zestaw .NET Core SDK jest już zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="b833c-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="b833c-117">Sprawdź poprawność instalacji zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="b833c-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="b833c-118">Otwórz powłokę poleceń i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="b833c-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="b833c-119">Zalecane narzędzia (tylko system Windows)</span><span class="sxs-lookup"><span data-stu-id="b833c-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="b833c-120">Zaawansowane narzędzia platformy Azure dla [programu Visual Studio](https://visualstudio.microsoft.com)zapewniają graficzny interfejs użytkownika dla większości funkcji opisanych w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="b833c-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="b833c-121">Będzie można korzystać z dowolnej wersji programu Visual Studio, w tym bezpłatnej wersji programu Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="b833c-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="b833c-122">Samouczki są zapisywane w taki sposób, aby demonstrować programowanie, wdrażanie i DevOps zarówno z programem Visual Studio, jak i bez niego.</span><span class="sxs-lookup"><span data-stu-id="b833c-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="b833c-123">Upewnij się, że program Visual Studio ma zainstalowane następujące [obciążenia](/visualstudio/install/modify-visual-studio) :</span><span class="sxs-lookup"><span data-stu-id="b833c-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="b833c-124">Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="b833c-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="b833c-125">Tworzenie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="b833c-125">Azure development</span></span>
  * <span data-ttu-id="b833c-126">Tworzenie aplikacji dla wielu platform w środowisku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b833c-126">.NET Core cross-platform development</span></span>
