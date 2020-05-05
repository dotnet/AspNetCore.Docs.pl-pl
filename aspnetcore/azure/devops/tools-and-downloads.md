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
ms.openlocfilehash: 321d506760d057914136f77c15e85043fa9d9832
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766540"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="98432-103">Narzędzia i pliki do pobrania</span><span class="sxs-lookup"><span data-stu-id="98432-103">Tools and downloads</span></span>

<span data-ttu-id="98432-104">Platforma Azure ma kilka interfejsów do aprowizacji zasobów i zarządzania nimi, takich jak [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)i programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="98432-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="98432-105">Ten przewodnik przyjmuje podejście minimalistyczny i używa Azure Cloud Shell, gdy jest to możliwe, aby zmniejszyć wymaganą procedurę.</span><span class="sxs-lookup"><span data-stu-id="98432-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="98432-106">Jednak Azure Portal muszą być używane w niektórych częściach.</span><span class="sxs-lookup"><span data-stu-id="98432-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="98432-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="98432-107">Prerequisites</span></span>

<span data-ttu-id="98432-108">Wymagane są następujące subskrypcje:</span><span class="sxs-lookup"><span data-stu-id="98432-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="98432-109">&mdash; Jeśli nie masz konta, możesz [skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="98432-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="98432-110">Azure DevOps Services &mdash; subskrypcję platformy Azure DevOps i organizacja jest tworzona w rozdziale 4.</span><span class="sxs-lookup"><span data-stu-id="98432-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="98432-111">GitHub &mdash; , jeśli nie masz konta, [zarejestruj się bezpłatnie](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="98432-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="98432-112">Wymagane są następujące narzędzia:</span><span class="sxs-lookup"><span data-stu-id="98432-112">The following tools are required:</span></span>

* <span data-ttu-id="98432-113">W tym przewodniku zalecamy podstawowe zrozumienie [usługi git.](https://git-scm.com/downloads) &mdash;</span><span class="sxs-lookup"><span data-stu-id="98432-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="98432-114">Przejrzyj [dokumentację usługi git](https://git-scm.com/doc), w tym [narzędzia Git Remote](https://git-scm.com/docs/git-remote) i [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="98432-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="98432-115">Do kompilowania i uruchamiania przykładowej aplikacji wymagany jest [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; w wersji 2.1.300 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="98432-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="98432-116">Jeśli program Visual Studio jest zainstalowany z obciążeniem **programistycznym dla wielu platform .NET Core** , zestaw .NET Core SDK jest już zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="98432-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="98432-117">Sprawdź poprawność instalacji zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="98432-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="98432-118">Otwórz powłokę poleceń i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="98432-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="98432-119">Zalecane narzędzia (tylko system Windows)</span><span class="sxs-lookup"><span data-stu-id="98432-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="98432-120">Zaawansowane narzędzia platformy Azure dla [programu Visual Studio](https://visualstudio.microsoft.com)zapewniają graficzny interfejs użytkownika dla większości funkcji opisanych w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="98432-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="98432-121">Będzie można korzystać z dowolnej wersji programu Visual Studio, w tym bezpłatnej wersji programu Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="98432-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="98432-122">Samouczki są zapisywane w taki sposób, aby demonstrować programowanie, wdrażanie i DevOps zarówno z programem Visual Studio, jak i bez niego.</span><span class="sxs-lookup"><span data-stu-id="98432-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="98432-123">Upewnij się, że program Visual Studio ma zainstalowane następujące [obciążenia](/visualstudio/install/modify-visual-studio) :</span><span class="sxs-lookup"><span data-stu-id="98432-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="98432-124">Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="98432-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="98432-125">Tworzenie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="98432-125">Azure development</span></span>
  * <span data-ttu-id="98432-126">Tworzenie aplikacji dla wielu platform w środowisku .NET Core</span><span class="sxs-lookup"><span data-stu-id="98432-126">.NET Core cross-platform development</span></span>
