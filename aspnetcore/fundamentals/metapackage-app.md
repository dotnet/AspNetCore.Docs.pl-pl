---
title: Pakiet Microsoft. AspNetCore. App dla ASP.NET Core
author: Rick-Anderson
description: Współdzielona platforma Microsoft. AspNetCore. App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/metapackage-app
ms.openlocfilehash: 89f447699e8219ec4dd48b2342095d86a011860f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407970"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft. AspNetCore. App dla ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 ASP.NET Core Shared Framework ( `Microsoft.AspNetCore.App` ) zawiera zestawy, które są opracowywane i obsługiwane przez firmę Microsoft. `Microsoft.AspNetCore.App`Program jest instalowany podczas instalowania [zestawu SDK platformy .NET Core 3,0 lub nowszego](https://dotnet.microsoft.com/download/dotnet-core/3.0) . *Platforma udostępniona* to zestaw zestawów (plików*dll* ), które są zainstalowane na komputerze i zawiera składnik środowiska uruchomieniowego oraz pakiet docelowy. Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projekty przeznaczone dla `Microsoft.NET.Sdk.Web` zestawu SDK niejawnie odwołują się do `Microsoft.AspNetCore.App` struktury.

Dla tych projektów nie są wymagane żadne dodatkowe odwołania:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

ASP.NET Core współdzielona struktura:

* Nie obejmuje zależności innych firm.
* Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ta funkcja wymaga ASP.NET Core 2. x przeznaczonych dla platformy .NET Core 2. x.

Pakiet [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) dla ASP.NET Core:

* Nie obejmuje zależności innych firm, z wyjątkiem [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [remotion. LINQ](https://www.nuget.org/packages/Remotion.Linq/)i [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Te zależności innych firm są uważane za niezbędne do zapewnienia funkcji głównych funkcji.
* Obejmuje wszystkie obsługiwane pakiety przez zespół ASP.NET Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wspomniane wcześniej).
* Obejmuje wszystkie obsługiwane pakiety przez zespół Entity Framework Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wspomniane wcześniej).

Wszystkie funkcje ASP.NET Core 2. x i Entity Framework Core 2. x są zawarte w `Microsoft.AspNetCore.App` pakiecie. Domyślne szablony projektu dla ASP.NET Core 2. x używają tego pakietu. Zalecamy stosowanie aplikacji przeznaczonych dla ASP.NET Core 2. x i Entity Framework Core 2. x `Microsoft.AspNetCore.App` .

Numer wersji `Microsoft.AspNetCore.App` pakietubinding reprezentuje minimalną wersję ASP.NET Core i Entity Framework Core.

Użycie `Microsoft.AspNetCore.App` pakietu z pakietem zawiera ograniczenia wersji chroniące Twoją aplikację:

* W przypadku dołączenia pakietu, który ma zależność przechodnią (nie bezpośrednią) w pakiecie w programie `Microsoft.AspNetCore.App` , a numery wersji różnią się, pakiet NuGet wygeneruje błąd.
* Inne pakiety dodane do aplikacji nie mogą zmienić wersji pakietów uwzględnionych w programie `Microsoft.AspNetCore.App` .
* Spójność wersji zapewnia niezawodne środowisko pracy. `Microsoft.AspNetCore.App`została zaprojektowana tak, aby uniemożliwić nietestowaną kombinację pokrewnych bitów w tej samej aplikacji.

Aplikacje korzystające z `Microsoft.AspNetCore.App` pakietubinding automatycznie wykorzystują ASP.NET Core udostępnionej platformy. W przypadku korzystania z `Microsoft.AspNetCore.App` pakietubinding nie są wdrażane **żadne** zasoby z przywoływanych ASP.NET Core pakietów NuGet z aplikacją &mdash; , ASP.NET Core udostępnione środowisko zawiera te zasoby. Zasoby w udostępnionej architekturze są wstępnie skompilowane w celu poprawienia czasu uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Następujący plik projektu odwołuje się do `Microsoft.AspNetCore.App` pakietu dla ASP.NET Core i reprezentuje typowy szablon ASP.NET Core 2,2:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Poprzedzający znacznik reprezentuje typowy szablon ASP.NET Core 2. x. Nie określa numeru wersji `Microsoft.AspNetCore.App` odwołania do pakietu. W przypadku nieokreślenia wersji [niejawna](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) wersja jest określana przez zestaw SDK, czyli `Microsoft.NET.Sdk.Web` . Zalecamy użycie niejawnej wersji określonej przez zestaw SDK i niejawne ustawienie numeru wersji w odwołaniu do pakietu. Jeśli masz pytania dotyczące tego podejścia, pozostaw komentarz w serwisie GitHub w [dyskusji dotyczącej wersji niejawnej Microsoft. AspNetCore. app](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Niejawna wersja jest ustawiona na `major.minor.0` dla aplikacji przenośnych. Mechanizm przekazujący przechodzenie do platformy udostępnionej będzie uruchamiał aplikację w najnowszej zgodnej wersji wśród zainstalowanych platform udostępnionych. Aby zagwarantować, że ta sama wersja jest używana w środowisku deweloperskim, testowym i produkcyjnym, upewnij się, że ta sama wersja udostępnionej platformy jest zainstalowana we wszystkich środowiskach. W przypadku aplikacji z własnym niejawnym numerem wersji jest ustawiana `major.minor.patch` Struktura udostępniona w zainstalowanym zestawie SDK.

Określenie numeru wersji w `Microsoft.AspNetCore.App` odwołaniu nie gwarantuje **not** , że zostanie wybrana wersja udostępnionej platformy. Na przykład załóżmy, że jest określona wersja "2.2.1", ale zainstalowano "2.2.3". W takim przypadku aplikacja będzie używać "2.2.3". Chociaż nie jest to zalecane, można wyłączyć funkcję wycofywania do przodu (poprawka i/lub pomocnicza). Aby uzyskać więcej informacji na temat przetworzenia i konfigurowania zachowań hosta dotnet, zobacz [przewinięcie hosta dotnet do przodu](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk`musi być ustawiona na, aby można było `Microsoft.NET.Sdk.Web` używać niejawnej wersji `Microsoft.AspNetCore.App` . Gdy `<Project Sdk="Microsoft.NET.Sdk">` jest używane (bez końcowego `.Web` ):

* Zostanie wygenerowane następujące ostrzeżenie:

  *Ostrzeżenie NU1604: zależność projektu Microsoft. AspNetCore. App nie zawiera mniejszej granicy. Uwzględnij dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*

* Jest to znany problem z zestawem SDK platformy .NET Core 2,1.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>ASP.NET Core aktualizacji

`Microsoft.AspNetCore.App` [Pakiet](/dotnet/core/packages#metapackages) nie jest tradycyjnym pakietem, który został zaktualizowany przy użyciu narzędzia NuGet. Podobnie jak `Microsoft.NETCore.App` , `Microsoft.AspNetCore.App` reprezentuje udostępnione środowisko uruchomieniowe, które ma specjalną semantykę wersji obsłużoną poza pakietem NuGet. Aby uzyskać więcej informacji, zobacz [pakiety, aplikacje i struktury](/dotnet/core/packages).

Aby zaktualizować ASP.NET Core:

* Na komputerach deweloperskich i serwerach kompilacji: Pobierz i zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).
* Na serwerach wdrażania: Pobierz i zainstaluj [środowisko uruchomieniowe programu .NET Core](https://dotnet.microsoft.com/download).

 Aplikacje zostaną przesunięte do najnowszej zainstalowanej wersji przy ponownym uruchomieniu aplikacji. Nie trzeba aktualizować `Microsoft.AspNetCore.App` numeru wersji w pliku projektu. Aby uzyskać więcej informacji, zobacz [aplikacje zależne od platformy — przewinięcie do przodu](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Jeśli aplikacja była wcześniej używana `Microsoft.AspNetCore.All` , zobacz [Migrowanie z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](xref:fundamentals/metapackage#migrate).

::: moniker-end
