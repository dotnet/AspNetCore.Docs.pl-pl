---
title: Migrowanie konfiguracji do ASP.NET Core
author: ardalis
description: Dowiedz się, jak przeprowadzić migrację konfiguracji z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: f65db927d79224695861101aff00897315c6e0b2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777231"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrowanie konfiguracji do ASP.NET Core

[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://scottaddie.com)

W poprzednim artykule rozpocząłmy [migrację projektu ASP.NET MVC do ASP.NET Core MVC](xref:migration/mvc). W tym artykule Migrujemy konfigurację.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Konfiguracja konfiguracji

ASP.NET Core nie używa już plików *Global. asax* i *Web. config* , z których korzysta poprzednie wersje ASP.NET. We wcześniejszych wersjach programu ASP.NET logika uruchamiania aplikacji została umieszczona w `Application_StartUp` metodzie w *Global. asax*. Później w ASP.NET MVC plik *Startup.cs* został uwzględniony w katalogu głównym projektu; i, został wywołany podczas uruchamiania aplikacji. ASP.NET Core częściowo przyjęła to podejście, umieszczając w pliku *Startup.cs* wszystkie logikę uruchamiania.

Plik *Web. config* został również zastąpiony w ASP.NET Core. Konfigurację można teraz skonfigurować w ramach procedury uruchamiania aplikacji opisanej w *Startup.cs*. Konfiguracja może nadal korzystać z plików XML, ale zazwyczaj projekty ASP.NET Core umieściją wartości konfiguracyjne w pliku w formacie JSON, takim jak *appSettings. JSON*. System konfiguracji ASP.NET Core może również łatwo uzyskać dostęp do zmiennych środowiskowych, co może zapewnić bezpieczniejsze [i niezawodne lokalizację](xref:security/app-secrets) dla wartości specyficznych dla środowiska. Jest to szczególnie prawdziwe w przypadku wpisów tajnych, takich jak parametry połączenia i klucze interfejsu API, które nie powinny być zaewidencjonowane do kontroli źródła. Zobacz [Konfiguracja](xref:fundamentals/configuration/index) , aby dowiedzieć się więcej o konfiguracji w ASP.NET Core.

W tym artykule zaczynamy od częściowo zmigrowanego projektu ASP.NET Core z [poprzedniego artykułu](xref:migration/mvc). Aby skonfigurować konfigurację, Dodaj następujący Konstruktor i właściwość do pliku *Startup.cs* znajdującego się w katalogu głównym projektu:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Należy pamiętać, że w tym momencie plik *Startup.cs* nie zostanie skompilowany, ponieważ nadal będziemy musieli dodać następującą `using` instrukcję:

```csharp
using Microsoft.Extensions.Configuration;
```

Dodaj plik *appSettings. JSON* do katalogu głównego projektu przy użyciu odpowiedniego szablonu elementu:

![Dodawanie pliku JSON AppSettings](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migruj ustawienia konfiguracji z pliku Web. config

Nasze projekty MVC ASP.NET zawierają wymagane parametry połączenia z bazą danych w *pliku Web. config*w `<connectionStrings>` elemencie. W naszym ASP.NET Core projekcie będziemy przechowywać te informacje w pliku *appSettings. JSON* . Otwórz plik *appSettings. JSON*i zwróć uwagę, że zawiera on już następujące elementy:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

W wyróżnionym wierszu poniżej Zmień nazwę bazy danych z **_CHANGE_ME** na nazwę bazy danych.

## <a name="summary"></a>Podsumowanie

ASP.NET Core umieszcza wszystkie logikę uruchamiania aplikacji w pojedynczym pliku, w którym można zdefiniować i skonfigurować wymagane usługi i zależności. Zastępuje plik *Web. config* elastyczną funkcją konfiguracji, która może korzystać z różnych formatów plików, takich jak JSON, a także zmiennych środowiskowych.
