---
title: Microsoft.AspNetCore.App metapackage dla ASP.NET Core
author: Rick-Anderson
description: Struktura współużytkowana Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511382"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App dla ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 Wspólna struktura ASP.NET Core`Microsoft.AspNetCore.App`( ) zawiera zestawy, które są opracowywane i obsługiwane przez firmę Microsoft. `Microsoft.AspNetCore.App`jest instalowany po zainstalowaniu [pliku .NET Core 3.0 lub nowszego SDK.](https://dotnet.microsoft.com/download/dotnet-core/3.0) *Współużytkowana struktura* to zestaw zestawów (plików*dll),* które są zainstalowane na komputerze i zawierają składnik środowiska wykonawczego i pakiet docelowy. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* Projekty, które `Microsoft.NET.Sdk.Web` są przeznaczone dla `Microsoft.AspNetCore.App` SDK niejawnie odwołać się do struktury.

W przypadku tych projektów nie są wymagane żadne dodatkowe odniesienia:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

Współdzielone ramy ASP.NET Core:

* Nie obejmuje zależności innych firm.
* Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ta funkcja wymaga ASP.NET celu Core 2.x .NET Core 2.x.

[Metapakiet](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) dla ASP.NET Core:

* Nie obejmuje zależności innych firm z wyjątkiem [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)i [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/). Te zależności innych firm są uważane za niezbędne do zapewnienia funkcji głównych struktur.
* Zawiera wszystkie obsługiwane pakiety przez zespół ASP.NET Core, z wyjątkiem tych, które zawierają zależności innych firm (inne niż wcześniej wymienione).
* Zawiera wszystkie obsługiwane pakiety przez zespół Entity Framework Core z wyjątkiem tych, które zawierają zależności innych firm (inne niż wcześniej wymienione).

Wszystkie funkcje ASP.NET Core 2.x i Entity Framework Core 2.x `Microsoft.AspNetCore.App` są zawarte w pakiecie. Domyślne szablony projektów przeznaczone dla ASP.NET Core 2.x używają tego pakietu. Zalecamy aplikacje przeznaczone ASP.NET Core 2.x i Entity Framework Core `Microsoft.AspNetCore.App` 2.x używać pakietu.

Numer wersji `Microsoft.AspNetCore.App` metapakietu reprezentuje minimalną ASP.NET wersję Core i wersję Core programu Entity Framework.

Korzystanie `Microsoft.AspNetCore.App` z metapakiety zapewnia ograniczenia wersji, które chronią aplikację:

* Jeśli pakiet jest dołączony, który ma przechodnie (nie bezpośrednie) `Microsoft.AspNetCore.App`zależność od pakietu w , a te numery wersji różnią się, NuGet wygeneruje błąd.
* Inne pakiety dodane do aplikacji nie mogą `Microsoft.AspNetCore.App`zmieniać wersji pakietów zawartych w pliku .
* Spójność wersji zapewnia niezawodne środowisko. `Microsoft.AspNetCore.App`został zaprojektowany, aby zapobiec nieprzetestowanych kombinacji wersji powiązanych bitów używanych razem w tej samej aplikacji.

Aplikacje korzystające `Microsoft.AspNetCore.App` z metapakiety automatycznie korzystają z udostępnionej struktury ASP.NET Core. Korzystając z `Microsoft.AspNetCore.App` metapakietu, **żadne** zasoby z pakietów Core NuGet, do których&mdash;odwołuje się odwołuje się ASP.NET Core NuGet, nie są wdrażane z aplikacją, ASP.NET framework udostępniony core zawiera te zasoby. Zasoby w udostępnionej ramach są wstępnie kompilowane w celu zwiększenia czasu uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Następujący plik projektu odwołuje `Microsoft.AspNetCore.App` się do metapakiety dla ASP.NET Core i reprezentuje typowy szablon ASP.NET Core 2.2:

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

Poprzedni znacznik reprezentuje typowy szablon ASP.NET Core 2.x. Nie określa numeru wersji dla `Microsoft.AspNetCore.App` odwołania do pakietu. Gdy wersja nie jest określona, wersja [niejawna](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) jest określona przez SDK, czyli `Microsoft.NET.Sdk.Web`. Zaleca się poleganie na wersji niejawnej określonej przez SDK i nie jawnie ustawienie numeru wersji na odwołanie do pakietu. Jeśli masz pytania dotyczące tego podejścia, zostaw komentarz GitHub w [dyskusji dla microsoft.AspNetCore.App niejawna wersja](https://github.com/dotnet/AspNetCore.Docs/issues/6430).

Wersja niejawna `major.minor.0` jest ustawiona na aplikacje przenośne. Mechanizm przekazywania udostępnionej struktury będzie uruchamiał aplikację w najnowszej zgodnej wersji wśród zainstalowanych udostępnionych struktur. Aby zagwarantować, że ta sama wersja jest używana w programach, testach i produkcji, upewnij się, że ta sama wersja udostępnionej struktury jest zainstalowana we wszystkich środowiskach. W przypadku aplikacji zawierających samą siebie niejawny numer wersji jest ustawiony na `major.minor.patch` współużytkowane framework w pakiecie z zainstalowanym zestawem SDK.

Określenie numeru wersji w `Microsoft.AspNetCore.App` odwołaniu **nie** gwarantuje, że zostanie wybrana wersja udostępnionej struktury. Załóżmy na przykład, że określono wersję "2.2.1", ale jest zainstalowana "2.2.3". W takim przypadku aplikacja użyje "2.2.3". Chociaż nie jest to zalecane, można wyłączyć roll forward (łatka i/lub drobne). Aby uzyskać więcej informacji dotyczących roll-forward hosta dotnet i sposobu konfigurowania jego zachowania, zobacz [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk`musi być `Microsoft.NET.Sdk.Web` ustawiona na `Microsoft.AspNetCore.App`użycie wersji niejawnej . Gdy `<Project Sdk="Microsoft.NET.Sdk">` (bez spływu) stosuje się: `.Web`

* Generowane jest następujące ostrzeżenie:

  *Ostrzeżenie NU1604: Zależność projektu Microsoft.AspNetCore.App nie zawiera włącznie dolnej granicy. Dołącz dolną granicę w wersji zależności, aby zapewnić spójne wyniki przywracania.*

* Jest to znany problem z sdk .NET Core 2.1.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Aktualizacja ASP.NET rdzenia

`Microsoft.AspNetCore.App` [Metapakiet](/dotnet/core/packages#metapackages) nie jest tradycyjnym pakietem, który jest aktualizowany z NuGet. Podobne `Microsoft.NETCore.App`do `Microsoft.AspNetCore.App` , reprezentuje udostępnionego środowiska uruchomieniowego, który ma specjalne semantyki wersji obsługiwane poza NuGet. Aby uzyskać więcej informacji, zobacz [Pakiety, metapakiety i struktury](/dotnet/core/packages).

Aby zaktualizować ASP.NET Core:

* Na komputerach deweloperskich i budować serwery: Pobierz i zainstaluj [.NET Core SDK](https://dotnet.microsoft.com/download).
* Na serwerach wdrażania: Pobierz i zainstaluj [środowisko uruchomieniowe .NET Core](https://dotnet.microsoft.com/download).

 Aplikacje zostaną przeniesione do najnowszej zainstalowanej wersji po ponownym uruchomieniu aplikacji. Nie jest konieczne aktualizowanie `Microsoft.AspNetCore.App` numeru wersji w pliku projektu. Aby uzyskać więcej informacji, zobacz [Aplikacje zależne od struktury są przewijane do przodu](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Jeśli aplikacja była `Microsoft.AspNetCore.All`wcześniej używana , zobacz [Migrowanie z pliku Microsoft.AspNetCore.All do pliku Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).

::: moniker-end
