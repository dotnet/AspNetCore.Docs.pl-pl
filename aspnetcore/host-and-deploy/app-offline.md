---
title: Plik w trybie offline aplikacji (app_offline.htm)
author: rick-anderson
description: Dowiedz się, jak plik trybu offline aplikacji ( `app_offline.htm` ) działa z modułem ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431033"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="0ec3c-103">Plik w trybie offline aplikacji ( `app_offline.htm` )</span><span class="sxs-lookup"><span data-stu-id="0ec3c-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="0ec3c-104">Plik w trybie offline aplikacji ( `app_offline.htm` ) jest używany przez moduł ASP.NET Core do zamykania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="0ec3c-105">Jeśli plik o nazwie `app_offline.htm` zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="0ec3c-106">Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core zatrzyma uruchomiony proces.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="0ec3c-107">Gdy `app_offline.htm` plik jest obecny, moduł ASP.NET Core odpowiada na żądania, wysyłając z powrotem zawartość `app_offline.htm` pliku.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="0ec3c-108">Po `app_offline.htm` usunięciu pliku, następne żądanie uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="0ec3c-109">W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0ec3c-110">Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="0ec3c-111">Zablokowane pliki wdrożenia</span><span class="sxs-lookup"><span data-stu-id="0ec3c-111">Locked deployment files</span></span>

<span data-ttu-id="0ec3c-112">Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="0ec3c-113">Nie można zastąpić zablokowanych plików podczas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="0ec3c-114">`app_offline.htm` jest mechanizmem podstawowym do zwolnienia zablokowanych plików.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="0ec3c-115">`app_offline.htm` jest używany przez Web Deploy do prawidłowego zatrzymania i uruchomienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="0ec3c-116">`app_offline.htm` może być używany ręcznie do uruchamiania i zatrzymywania aplikacji (wymaga programu PowerShell 5 lub nowszego):</span><span class="sxs-lookup"><span data-stu-id="0ec3c-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

<span data-ttu-id="0ec3c-117">W poprzednim skrypcie programu PowerShell:</span><span class="sxs-lookup"><span data-stu-id="0ec3c-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="0ec3c-118">Symbol zastępczy `{PATH TO APP}` jest ścieżką do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="0ec3c-119">`New-Item`Polecenie powoduje zatrzymanie puli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="0ec3c-120">`Remove-Item`Polecenie uruchamia pulę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="0ec3c-121">Polecenia między `New-Item` poleceniem a `Remove-Item` poleceniem są udostępniane przez dewelopera w celu wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="0ec3c-122">Pliki można także odblokować, ręcznie zatrzymując pulę aplikacji w Menedżerze usług IIS na serwerze.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="0ec3c-123">Nie należy używać tego `app_offine.htm` pliku w przypadku zatrzymywania i ponownego uruchamiania puli aplikacji przy użyciu Menedżera usług IIS.</span><span class="sxs-lookup"><span data-stu-id="0ec3c-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
