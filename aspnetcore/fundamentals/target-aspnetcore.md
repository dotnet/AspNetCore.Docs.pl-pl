---
title: Używanie ASP.NET podstawowych interfejsów API w bibliotece klas
author: scottaddie
description: Dowiedz się, jak używać ASP.NET podstawowych interfejsów API w bibliotece klas.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977200"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Używanie ASP.NET podstawowych interfejsów API w bibliotece klas

Przez [Scott Addie](https://github.com/scottaddie)

Ten dokument zawiera wskazówki dotyczące używania ASP.NET podstawowych interfejsów API w bibliotece klas. Aby uzyskać wszystkie inne wskazówki dotyczące biblioteki, zobacz [Wskazówki dotyczące biblioteki open source](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Określanie, które ASP.NET wersje Core do obsługi

ASP.NET Core jest zgodny z [zasadami pomocy technicznej platformy .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Zapoznaj się z zasadami pomocy technicznej podczas określania, które ASP.NET wersje core do obsługi w bibliotece. Biblioteka powinna:

* Dołożyj starań, aby wspierać wszystkie ASP.NET wersje Core sklasyfikowane jako *obsługa długoterminowa* (LTS).
* Nie czują się zobowiązani do obsługi ASP.NET wersji Core sklasyfikowanych jako *End of Life* (EOL).

W wersji zapoznawczej ASP.NET Core są udostępniane, zmiany podziału są księgowane w repozytorium [Aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub. Testowanie zgodności bibliotek mogą być prowadzone w miarę opracowywania funkcji struktury.

## <a name="use-the-aspnet-core-shared-framework"></a>Korzystanie z udostępnionej struktury ASP.NET Core

Wraz z wydaniem platformy .NET Core 3.0 wiele ASP.NET zestawów core nie są już publikowane do NuGet jako pakiety. Zamiast tego zestawy są zawarte w `Microsoft.AspNetCore.App` udostępnionej ramach, która jest instalowana z instalatorami .NET Core SDK i runtime. Aby uzyskać listę pakietów, które nie są już publikowane, zobacz [Usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).

Od .NET Core 3.0 projekty `Microsoft.NET.Sdk.Web` przy użyciu SDK MSBuild niejawnie odwołują się do udostępnionej struktury. Projekty przy `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` użyciu lub SDK musi odwoływać się ASP.NET Core do korzystania ASP.NET podstawowych interfejsów API w udostępnionej ramach.

Aby odwołać się ASP.NET Core, `<FrameworkReference>` dodaj do pliku projektu następujący element:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Odwoływanie się ASP.NET Core w ten sposób jest obsługiwane tylko dla projektów przeznaczonych dla platformy .NET Core 3.x.

## <a name="include-blazor-extensibility"></a>Uwzględnij rozszerzalność blazora

Blazor obsługuje [modele hostingu](xref:blazor/hosting-models)WebAssembly (WASM) i Server. Jeśli nie ma konkretnego powodu, aby nie, biblioteka [składników Razor](xref:blazor/components) powinna obsługiwać oba modele hostingu. Biblioteka składników Razor musi używać pakietu [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Obsługa obu modeli hostingu

Aby obsługiwać zużycie komponentów Razor zarówno z projektów [Blazor Server,](xref:blazor/hosting-models#blazor-server) jak i [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) skorzystaj z poniższych instrukcji dla edytora.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Użyj szablonu projektu **Biblioteka klas Razor.** Pole wyboru **Strony pomocy technicznej i widoki** szablonu powinny zostać odznaczone.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w zintegrowanym terminalu:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Użyj szablonu projektu **Biblioteka klas Razor.**

---

Projekt wygenerowany na podstawie szablonu wykonuje następujące czynności:

* Obiekty docelowe .NET Standard 2.0.
* Ustawia `RazorLangVersion` właściwość `3.0`na . `3.0`jest wartością domyślną dla platformy .NET Core 3.x.
* Dodaje następujące odwołania do pakietu:
  * [Składniki Microsoft.AspNetCore.](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Obsługa określonego modelu hostingu

Znacznie rzadziej obsługuje jeden model hostingowy Blazor. Na przykład do obsługi zużycia komponentów Razor z [blazor server](xref:blazor/hosting-models#blazor-server) projektów tylko:

* Docelowy .NET Core 3.x.
* Dodaj `<FrameworkReference>` element dla udostępnionej struktury.

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Aby uzyskać więcej informacji na temat bibliotek zawierających składniki razor, zobacz [ASP.NET Biblioteki klas podstawowych składników razor](xref:blazor/class-libraries).

## <a name="include-mvc-extensibility"></a>Uwzględnij rozszerzalność MVC

W tej sekcji przedstawiono zalecenia dotyczące bibliotek, które obejmują:

* [Widoki maszynki do golenia lub strony maszynki do golenia](#razor-views-or-razor-pages)
* [Pomocnicy tagów](#tag-helpers)
* [Składniki widoków](#view-components)

W tej sekcji nie omówiono multi-targeting do obsługi wielu wersji MVC. Aby uzyskać wskazówki dotyczące obsługi wielu wersji ASP.NET Core, zobacz [Obsługa wielu ASP.NET wersji core](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Widoki maszynki do golenia lub strony maszynki do golenia

Projekt zawierający [widoki Razor](xref:mvc/views/overview) lub [strony Razor](xref:razor-pages/index) musi używać [sdk Microsoft.NET.Sdk.Razor.](xref:razor-pages/sdk)

Jeśli projekt jest przeznaczony dla .NET Core 3.x, wymaga:

* Właściwość `AddRazorSupportForMvc` MSBuild `true`ustawiona na .
* Element `<FrameworkReference>` dla wspólnej struktury.

Szablon projektu **Biblioteka klas Razor** spełnia poprzednie wymagania dotyczące projektów przeznaczonych dla platformy .NET Core 3.x. Użyj poniższych instrukcji dla edytora.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Użyj szablonu projektu **Biblioteka klas Razor.** Należy zaznaczyć pole wyboru **Strony pomocy technicznej i widoki** szablonu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w zintegrowanym terminalu:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Brak obsługi szablonu projektu w tej chwili.

---

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Jeśli projekt jest przeznaczony dla platformy .NET Standard, wymagane jest odwołanie do pakietu [Microsoft.AspNetCore.Mvc.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) Pakiet `Microsoft.AspNetCore.Mvc` został przeniesiony do udostępnionej struktury w ASP.NET Core 3.0 i dlatego nie jest już publikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Pomocnicy tagów

Projekt, który zawiera [Pomocników tagów](xref:mvc/views/tag-helpers/intro) należy użyć zestawu `Microsoft.NET.Sdk` SDK. Jeśli kierowanie .NET Core 3.x, dodać `<FrameworkReference>` element dla udostępnionej struktury. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Jeśli targetowanie .NET Standard (do obsługi wersji wcześniejszych niż ASP.NET Core 3.x), należy dodać odwołanie do pakietu [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). Pakiet `Microsoft.AspNetCore.Mvc.Razor` został przeniesiony do wspólnej struktury i dlatego nie jest już publikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Składniki widoku

Projekt, który zawiera [składniki](xref:mvc/views/view-components) `Microsoft.NET.Sdk` widoku należy użyć zestawu SDK. Jeśli kierowanie .NET Core 3.x, dodać `<FrameworkReference>` element dla udostępnionej struktury. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Jeśli targetowanie .NET Standard (do obsługi wersji wcześniejszych niż ASP.NET Core 3.x), dodaj odwołanie do pakietu [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). Pakiet `Microsoft.AspNetCore.Mvc.ViewFeatures` został przeniesiony do wspólnej struktury i dlatego nie jest już publikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Obsługa wielu wersji ASP.NET Core

Multi-targeting jest wymagane do tworzenia biblioteki, która obsługuje wiele wariantów ASP.NET Core. Rozważmy scenariusz, w którym biblioteka Pomocników tagów musi obsługiwać następujące ASP.NET warianty rdzenia:

* ASP.NET Core 2.1 ukierunkowany na .NET Framework 4.6.1
* ASP.NET Core 2.x kierowania .NET Core 2.x
* ASP.NET Core 3.x kierowania .NET Core 3.x

Następujący plik projektu obsługuje te `TargetFrameworks` warianty za pośrednictwem właściwości:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

W poprzednim pliku projektu:

* Pakiet `Markdig` jest dodawany dla wszystkich konsumentów.
* Odwołanie do [pliku Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) jest dodawane dla konsumentów przeznaczonych dla platformy .NET Framework 4.6.1 lub nowszych lub .NET Core 2.x. Wersja 2.1.0 pakietu współpracuje z ASP.NET Core 2.2 ze względu na zgodność z powrotem.
* Do udostępnionej struktury odwołuje się do konsumentów kierowanych na .NET Core 3.x. Pakiet `Microsoft.AspNetCore.Mvc.Razor` znajduje się w udostępnionej ramach.

Alternatywnie można kierować na program .NET Standard 2.0 zamiast kierować reklamy na program .NET Core 2.1 i .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

W poprzednim pliku projektu istnieją następujące zastrzeżenia:

* Ponieważ biblioteka zawiera tylko Pomocników tagów, jest bardziej proste do kierowania określonych platform, na których działa ASP.NET Core: .NET Core i .NET Framework. Pomocników tagów nie może być używany przez inne platformy docelowe zgodne ze standardem .NET Standard 2.0, takie jak Unity, UWP i Xamarin.
* Za pomocą .NET Standard 2.0 z .NET Framework ma pewne problemy, które zostały rozwiązane w .NET Framework 4.7.2. Można poprawić środowisko dla konsumentów korzystających z programu .NET Framework 4.6.1 do 4.7.1, kierując na .NET Framework 4.6.1.

Jeśli biblioteka musi wywoływać interfejsy API specyficzne dla platformy, należy kierować określone implementacje platformy .NET zamiast .NET Standard. Aby uzyskać więcej informacji, zobacz [Kierowanie zbiorcze](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Używanie interfejsu API, który się nie zmienił

Wyobraź sobie scenariusz, w którym uaktualniasz bibliotekę oprogramowania pośredniczącego z platformy .NET Core 2.2 do 3.0. Interfejsy API oprogramowania pośredniczącego ASP.NET Core używane w bibliotece nie uległy zmianie między ASP.NET Core 2.2 i 3.0. Aby kontynuować obsługę biblioteki oprogramowania pośredniczącego w .NET Core 3.0, należy wykonać następujące kroki:

* Postępuj zgodnie [ze standardowymi wskazówkami dotyczącymi biblioteki](/dotnet/standard/library-guidance/).
* Dodaj odwołanie do pakietu dla każdego pakietu NuGet interfejsu API, jeśli odpowiedni zestaw nie istnieje w udostępnionej ramach.

## <a name="use-an-api-that-changed"></a>Używanie interfejsu API, który uległ zmianie

Wyobraź sobie scenariusz, w którym uaktualniasz bibliotekę z platformy .NET Core 2.2 do .NET Core 3.0. Interfejs API ASP.NET Core używany w bibliotece ma [przełomową zmianę](/dotnet/core/compatibility/breaking-changes) w ASP.NET Core 3.0. Należy wziąć pod uwagę, czy biblioteki można przepisać, aby nie używać uszkodzonego interfejsu API we wszystkich wersjach.

Jeśli można przepisać biblioteki, to zrobić i nadal kierować wcześniej platformę docelową (na przykład .NET Standard 2.0 lub .NET Framework 4.6.1) z odwołaniami do pakietu.

Jeśli nie można przepisać biblioteki, wykonać następujące czynności:

* Dodaj obiekt docelowy dla platformy .NET Core 3.0.
* Dodaj `<FrameworkReference>` element dla udostępnionej struktury.
* Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem struktury docelowej, aby warunkowo skompilować kod.

Na przykład synchroniczne odczyty i zapisy w strumieniach żądań i odpowiedzi HTTP są domyślnie wyłączone od ASP.NET Core 3.0. ASP.NET Core 2.2 domyślnie obsługuje zachowanie synchroniczne. Należy wziąć pod uwagę biblioteki oprogramowania pośredniczącego, w którym synchroniczne odczyty i zapisy powinny być włączone, gdzie występują we/wy. Biblioteka należy dołączyć kod, aby włączyć funkcje synchroniczne w odpowiedniej dyrektywy preprocesora. Przykład:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Użyj interfejsu API wprowadzonego w 3.0

Wyobraź sobie, że chcesz użyć ASP.NET core API, który został wprowadzony w ASP.NET Core 3.0. Zastanów się nad następującymi pytaniami:

1. Czy biblioteka funkcjonalnie wymaga nowego interfejsu API?
1. Czy biblioteka może zaimplementować tę funkcję w inny sposób?

Jeśli biblioteka funkcjonalnie wymaga interfejsu API i nie ma sposobu, aby zaimplementować go w dół:

* Tylko docelowy .NET Core 3.x.
* Dodaj `<FrameworkReference>` element dla udostępnionej struktury.

Jeśli biblioteka może zaimplementować operację w inny sposób:

* Dodaj .NET Core 3.x jako platformę docelową.
* Dodaj `<FrameworkReference>` element dla udostępnionej struktury.
* Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem struktury docelowej, aby warunkowo skompilować kod.

Na przykład następujące Pomocnik tagów <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> używa interfejsu wprowadzonego w ASP.NET Core 3.0. Konsumenci kierowani na program .NET Core 3.0 `NETCOREAPP3_0` wykonują ścieżkę kodu zdefiniowaną przez symbol struktury docelowej. Typ parametru konstruktora pomocnika <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> tagu zmienia się na dla konsumentów .NET Core 2.1 i .NET Framework 4.6.1. Ta zmiana była konieczna, ponieważ ASP.NET Core `IHostingEnvironment` 3.0 oznaczone jako przestarzałe i zalecane `IWebHostEnvironment` jako zamiennik.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Następujący plik projektu z wieloma celami obsługuje ten scenariusz Pomocnika znaczników:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Używanie interfejsu API usuniętego z udostępnionej struktury

Aby użyć zestawu ASP.NET Core, który został usunięty z udostępnionej struktury, dodaj odpowiednie odwołanie do pakietu. Aby uzyskać listę pakietów usuniętych z udostępnionej struktury w ASP.NET Core 3.0, zobacz [Usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).

Na przykład, aby dodać klienta interfejsu API sieci Web:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [Pomoc techniczna w zakresie implementacji platformy .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Zasady pomocy technicznej platformy .NET](https://dotnet.microsoft.com/platform/support/policy)
