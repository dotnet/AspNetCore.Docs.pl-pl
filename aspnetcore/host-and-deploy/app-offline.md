---
title: Plik w trybie offline aplikacji (app_offline.htm)
author: rick-anderson
description: Dowiedz się, jak plik trybu offline aplikacji ( `app_offline.htm` ) działa z modułem ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755300"
---
# <a name="app-offline-file-app_offlinehtm"></a>Plik w trybie offline aplikacji ( `app_offline.htm` )

Plik w trybie offline aplikacji ( `app_offline.htm` ) jest używany przez moduł ASP.NET Core do zamykania aplikacji.

Jeśli plik o nazwie `app_offline.htm` zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących. Jeśli aplikacja nadal działa po upływie liczby sekund zdefiniowanej w programie `shutdownTimeLimit` , moduł ASP.NET Core zatrzyma uruchomiony proces.

Gdy `app_offline.htm` plik jest obecny, moduł ASP.NET Core odpowiada na żądania, wysyłając z powrotem zawartość `app_offline.htm` pliku. Po `app_offline.htm` usunięciu pliku, następne żądanie uruchamia aplikację.

W przypadku korzystania z modelu hostingu poza procesem aplikacja może nie zostać wyłączona natychmiast, jeśli istnieje otwarte połączenie. Na przykład połączenie protokołu WebSocket może opóźnić zamykanie aplikacji.

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrożenia

Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona. Nie można zastąpić zablokowanych plików podczas wdrażania.

`app_offline.htm` jest mechanizmem podstawowym do zwolnienia zablokowanych plików. `app_offline.htm` jest używany przez Web Deploy do prawidłowego zatrzymania i uruchomienia aplikacji.

`app_offline.htm` może być używany ręcznie do uruchamiania i zatrzymywania aplikacji (wymaga programu PowerShell 5 lub nowszego):

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

W poprzednim skrypcie programu PowerShell:

* Symbol zastępczy `{PATH TO APP}` jest ścieżką do aplikacji.
* `New-Item`Polecenie powoduje zatrzymanie puli aplikacji.
* `Remove-Item`Polecenie uruchamia pulę aplikacji.
* Polecenia między `New-Item` poleceniem a `Remove-Item` poleceniem są udostępniane przez dewelopera w celu wdrożenia aplikacji.

Pliki można także odblokować, ręcznie zatrzymując pulę aplikacji w Menedżerze usług IIS na serwerze. Nie należy używać tego `app_offine.htm` pliku w przypadku zatrzymywania i ponownego uruchamiania puli aplikacji przy użyciu Menedżera usług IIS.
