---
title: RazorBiblioteki klas składników ASP.NET Core
author: guardrex
description: Odkryj, jak składniki mogą być dołączane do Blazor aplikacji z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252555"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorBiblioteki klas składników ASP.NET Core

Autor [Simon Timms](https://github.com/stimms)

Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach. *Razor Biblioteka klas składników* może być dołączana z:

* Inny projekt w rozwiązaniu.
* Pakiet NuGet.
* Przywoływana Biblioteka platformy .NET.

Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.

## <a name="create-an-rcl"></a>Utwórz RCL

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.
1. Wybierz **Razor bibliotekę klas**. Wybierz pozycję **Dalej**.
1. W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.
1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. W przykładach w tym temacie użyto nazwy projektu `ComponentLibrary` . Wybierz przycisk **Utwórz**.
1. Dodaj RCL do rozwiązania:
   1. Kliknij prawym przyciskiem myszy rozwiązanie. Wybierz pozycję **Dodaj**  >  **istniejący projekt**.
   1. Przejdź do pliku projektu RCL.
   1. Wybierz plik projektu RCL ( `.csproj` ).
1. Dodaj odwołanie do RCL z aplikacji:
   1. Kliknij prawym przyciskiem myszy projekt aplikacji. Wybierz pozycję **Dodaj**  >  **odwołanie**.
   1. Wybierz projekt RCL. Wybierz przycisk **OK**.

> [!NOTE]
> Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

1. Użyj szablonu **Razor biblioteki klas** ( `razorclasslib` ) za pomocą [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenia w powłoce poleceń. W poniższym przykładzie jest tworzony RCL o nazwie `ComponentLibrary` . Folder, który `ComponentLibrary` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również `_Imports.razor` plik do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.

1. Aby dodać bibliotekę do istniejącego projektu, użyj [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) polecenia w powłoce poleceń. W poniższym przykładzie RCL jest dodawany do aplikacji. Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Korzystanie ze składnika biblioteki

Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:

* Użyj pełnej nazwy typu z przestrzeni nazw.
* Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę. Poszczególne składniki można dodawać według nazwy.

W poniższych przykładach `ComponentLibrary` jest biblioteka składników zawierająca `Component1` składnik ( `Component1.razor` ). `Component1`Składnik jest przykładowym składnikiem automatycznie dodawanym przez szablon projektu RCL podczas tworzenia biblioteki.

Odwołuje się do `Component1` składnika przy użyciu jego przestrzeni nazw:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Alternatywnie można przenieść bibliotekę do zakresu za pomocą [`@using`](xref:mvc/views/razor#using) dyrektywy i użyć składnika bez jego przestrzeni nazw:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Opcjonalnie należy uwzględnić `@using ComponentLibrary` dyrektywę w pliku najwyższego poziomu, `_Import.razor` Aby udostępnić składniki biblioteki dla całego projektu. Dodaj dyrektywę do pliku na `_Import.razor` dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczego składnika lub zestawu składników w folderze.

::: moniker range=">= aspnetcore-5.0"

W przypadku składników biblioteki, które korzystają z [izolacji CSS](xref:blazor/components/css-isolation), nie istnieje potrzeba jawnego łączenia stylów poszczególnych składników biblioteki w aplikacji, która korzysta z biblioteki. Style składników są automatycznie udostępniane aplikacji zużywanej.

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

Aby zapewnić dodatkowe style składników biblioteki z arkuszy stylów w `wwwroot` folderze biblioteki, Połącz arkusze stylów w `wwwroot/index.html` pliku aplikacji ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ).

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Aby podać `Component1` `my-component` klasę CSS, Połącz się z arkuszem stylów biblioteki w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku () aplikacji ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Tworzenie Razor biblioteki klas składników ze statycznymi zasobami

RCL może zawierać statyczne zasoby. Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Dostarczaj składniki i zasoby statyczne do wielu Blazor aplikacji hostowanych

Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Analizator zgodności przeglądarki dla programu Blazor WebAssembly

Blazor WebAssembly aplikacje są przeznaczone dla całego obszaru powierzchni interfejsu API platformy .NET, ale nie wszystkie interfejsy API platformy .NET są obsługiwane w zestawie webassembly z powodu ograniczeń piaskownicy przeglądarki. Nieobsługiwane interfejsy API są zgłaszane <xref:System.PlatformNotSupportedException> podczas uruchamiania w zestawie webassembly. Analizator zgodności platformy ostrzega dewelopera, gdy aplikacja korzysta z interfejsów API, które nie są obsługiwane przez Platformy docelowe aplikacji. W przypadku Blazor WebAssembly aplikacji oznacza to sprawdzenie, czy interfejsy API są obsługiwane w przeglądarkach. Dodawanie adnotacji do interfejsów API programu .NET Framework dla analizatora zgodności jest procesem trwającym, więc nie wszystkie interfejsy API programu .NET Framework są obecnie opatrzone adnotacją.

Blazor WebAssembly i Razor projekty biblioteki klas *automatycznie* włączają testy compatibilty przeglądarki przez dodanie `browser` jako obsługiwanej platformy z `SupportedPlatform` elementem MSBuild. Deweloperzy biblioteki mogą ręcznie dodać `SupportedPlatform` element do pliku projektu biblioteki, aby włączyć tę funkcję:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Podczas tworzenia biblioteki należy wskazać, że konkretny interfejs API nie jest obsługiwany w przeglądarkach, określając `browser` <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Aby uzyskać więcej informacji, zobacz [Dodawanie adnotacji do interfejsów API jako nieobsługiwanych na określonych platformach (repozytorium dotnet/Designing w witrynie GitHub](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Izolacja kodu JavaScript i odwołania do obiektów

Blazor Włącza izolację JavaScript w standardowych [modułach języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Izolacja JavaScript zapewnia następujące korzyści:

* Zaimportowana wartość JavaScript nie jest już zanieczyszczana globalną przestrzenią nazw.
* Odbiorcy biblioteki i składników nie są zobowiązani do ręcznego zaimportowania powiązanego języka JavaScript.

Aby uzyskać więcej informacji, zobacz <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet

Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet. Pakowanie jest wykonywane przy użyciu [`dotnet pack`](/dotnet/core/tools/dotnet-pack) polecenia w powłoce poleceń:

```dotnetcli
dotnet pack
```

Przekaż pakiet do narzędzia NuGet przy użyciu [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) polecenia w powłoce poleceń.

## <a name="additional-resources"></a>Dodatkowe zasoby

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Dodawanie pliku konfiguracji programu do biblioteki języka pośredniego (IL) w formacie XML](xref:blazor/host-and-deploy/configure-trimmer)
* [Obsługa izolacji CSS z Razor bibliotekami klas](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Dodawanie pliku konfiguracji konsolidatora języka pośredniego (IL) XML do biblioteki](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
