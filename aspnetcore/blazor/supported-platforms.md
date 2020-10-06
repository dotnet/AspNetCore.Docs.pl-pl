---
title: ASP.NET Core Blazor obsługiwane platformy
author: guardrex
description: Dowiedz się więcej na temat obsługiwanych platform dla ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754544"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor obsługiwane platformy

Autor [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly i Blazor Server są obsługiwane w przeglądarkach przedstawionych w poniższej tabeli.

| Przeglądarka                          | Wersja         |
| -------------------------------- | --------------- |
| Apple Safari, w tym iOS      | Obecne&dagger; |
| Google Chrome, w tym Android | Obecne&dagger; |
| Microsoft Edge                   | Obecne&dagger; |
| Mozilla Firefox                  | Obecne&dagger; |  

&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Przeglądarka                          | Wersja               |
| -------------------------------- | --------------------- |
| Apple Safari, w tym iOS      | Obecne&dagger;       |
| Google Chrome, w tym Android | Obecne&dagger;       |
| Microsoft Edge                   | Obecne&dagger;       |
| Microsoft Internet Explorer      | Nieobsługiwane&Dagger; |
| Mozilla Firefox                  | Obecne&dagger;       |  

&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.  
&Dagger;Program Microsoft Internet Explorer nie obsługuje [zestawu webassembly](https://webassembly.org).

## Blazor Server

| Przeglądarka                          | Wersja         |
| -------------------------------- | --------------- |
| Apple Safari, w tym iOS      | Obecne&dagger; |
| Google Chrome, w tym Android | Obecne&dagger; |
| Microsoft Edge                   | Obecne&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Obecne&dagger; |

&dagger;*Bieżąca* odwołuje się do najnowszej wersji przeglądarki.  
&Dagger;Dodatkowe wypełnienia są wymagane. Na przykład niesie obietnice zwiększenia można dodać za pośrednictwem [`Polyfill.io`](https://polyfill.io/v3/) pakietu.

::: moniker-end

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
