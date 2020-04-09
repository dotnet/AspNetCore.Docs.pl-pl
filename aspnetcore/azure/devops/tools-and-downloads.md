---
title: Narzędzia i pliki do pobrania — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Narzędzia i pliki do pobrania wymagane dla devops z ASP.NET Core i platformy Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511148"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="d0296-103">Narzędzia i pliki do pobrania</span><span class="sxs-lookup"><span data-stu-id="d0296-103">Tools and downloads</span></span>

<span data-ttu-id="d0296-104">Platforma Azure ma kilka interfejsów do inicjowania obsługi administracyjnej zasobów i zarządzania nimi, takich jak [witryna Azure portal](https://portal.azure.com), [interfejs wiersza polecenia platformy Azure,](/cli/azure/) [usługa Azure PowerShell,](/powershell/azure/overview) [usługa Azure Cloud Shell](https://shell.azure.com/bash)i program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d0296-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="d0296-105">Ten przewodnik przyjmuje minimalistyczne podejście i używa usługi Azure Cloud Shell, gdy tylko jest to możliwe, aby zmniejszyć wymagane kroki.</span><span class="sxs-lookup"><span data-stu-id="d0296-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="d0296-106">Jednak witryny Azure portal musi być używany dla niektórych części.</span><span class="sxs-lookup"><span data-stu-id="d0296-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0296-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="d0296-107">Prerequisites</span></span>

<span data-ttu-id="d0296-108">Wymagane są następujące subskrypcje:</span><span class="sxs-lookup"><span data-stu-id="d0296-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="d0296-109">Platforma &mdash; Azure Jeśli nie masz konta, [uzyskaj bezpłatną wersję próbną.](https://azure.microsoft.com/free/)</span><span class="sxs-lookup"><span data-stu-id="d0296-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="d0296-110">Usługi Azure DevOps &mdash; subskrypcji i organizacji Usługi Azure DevOps jest tworzony w rozdziale 4.</span><span class="sxs-lookup"><span data-stu-id="d0296-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="d0296-111">GitHub &mdash; Jeśli nie masz konta, [zarejestruj się za darmo](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="d0296-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="d0296-112">Wymagane są następujące narzędzia:</span><span class="sxs-lookup"><span data-stu-id="d0296-112">The following tools are required:</span></span>

* <span data-ttu-id="d0296-113">[Git](https://git-scm.com/downloads) &mdash; Podstawowe zrozumienie Git jest zalecane dla tego przewodnika.</span><span class="sxs-lookup"><span data-stu-id="d0296-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="d0296-114">Przejrzyj [dokumentację Git](https://git-scm.com/doc), w szczególności [git remote](https://git-scm.com/docs/git-remote) i [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="d0296-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="d0296-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; w wersji 2.1.300 lub nowszej jest wymagane do tworzenia i uruchamiania przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0296-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="d0296-116">Jeśli program Visual Studio jest zainstalowany z **wieloplatformowym obciążeniem deweloperskim .NET Core,** pakiet .NET Core SDK jest już zainstalowany.</span><span class="sxs-lookup"><span data-stu-id="d0296-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="d0296-117">Sprawdź instalację sdk .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0296-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="d0296-118">Otwórz powłokę polecenia i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d0296-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="d0296-119">Zalecane narzędzia (tylko windows)</span><span class="sxs-lookup"><span data-stu-id="d0296-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="d0296-120">Niezawodne narzędzia platformy Azure programu [Visual Studio](https://visualstudio.microsoft.com)zapewniają interfejs graficzny dla większości funkcji opisanych w tym przewodniku.</span><span class="sxs-lookup"><span data-stu-id="d0296-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="d0296-121">Każda wersja programu Visual Studio będzie działać, w tym bezpłatna wersja społeczności programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d0296-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="d0296-122">Samouczki są zapisywane w celu zademonstrowania rozwoju, wdrożenia i devops zarówno z i bez programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d0296-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="d0296-123">Upewnij się, że program Visual Studio ma zainstalowane następujące [obciążenia:](/visualstudio/install/modify-visual-studio)</span><span class="sxs-lookup"><span data-stu-id="d0296-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="d0296-124">Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="d0296-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="d0296-125">Tworzenie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="d0296-125">Azure development</span></span>
  * <span data-ttu-id="d0296-126">Tworzenie aplikacji dla wielu platform w środowisku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d0296-126">.NET Core cross-platform development</span></span>
