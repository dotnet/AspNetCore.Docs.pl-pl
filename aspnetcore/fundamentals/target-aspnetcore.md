---
title: Używanie ASP.NET Core interfejsów API w bibliotece klas
author: scottaddie
description: Dowiedz się, jak używać interfejsów API ASP.NET Core w bibliotece klas.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 70a445d109a1a9553178e94d79df87cd373e6b06
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103020"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Używanie ASP.NET Core interfejsów API w bibliotece klas

Przez [Scott Addie](https://github.com/scottaddie)

Ten dokument zawiera wskazówki dotyczące używania ASP.NET Core interfejsów API w bibliotece klas. Wszystkie inne wskazówki dotyczące biblioteki można znaleźć w temacie [wskazówki dotyczące biblioteki Open Source](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Określ, które wersje ASP.NET Core mają być obsługiwane

ASP.NET Core jest zgodna z [zasadami obsługi .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Podczas określania, które wersje ASP.NET Core mają być obsługiwane w bibliotece, należy zapoznać się z zasadami pomocy technicznej. Biblioteka powinna:

* Zwiększ nakład pracy, aby obsłużyć wszystkie ASP.NET Core wersje sklasyfikowane jako *długoterminowe wsparcie* (LTS).
* Nie jest zobowiązana do obsługi wersji ASP.NET Core sklasyfikowanych jako *koniec cyklu życia* (EOL).

W przypadku udostępnienia wersji zapoznawczej ASP.NET Core zostaną ogłoszone istotne zmiany w repozytorium GitHub [/anonsów](https://github.com/aspnet/Announcements/issues) w serwisie. Testowanie zgodności bibliotek można przeprowadzić w miarę opracowania funkcji platformy.

## <a name="use-the-aspnet-core-shared-framework"></a>Użyj ASP.NET Core udostępnionej platformy

W wersji programu .NET Core 3,0 wiele zestawów ASP.NET Core nie jest już publikowanych w pakiecie NuGet jako pakiety. Zamiast tego zestawy są zawarte w `Microsoft.AspNetCore.App` udostępnionej platformie, która jest instalowana z instalatorami zestaw .NET Core SDK i Runtime. Aby zapoznać się z listą pakietów, które nie są już publikowane, zobacz [usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).

Począwszy od platformy .NET Core 3,0, projekty używające `Microsoft.NET.Sdk.Web` zestawu MSBuild SDK niejawnie odwołują się do udostępnionej platformy. Projekty używające `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.Razor` zestawu SDK lub muszą odwoływać się do ASP.NET Core, aby używać interfejsów API ASP.NET Core w strukturze udostępnionej.

Aby odwołać się do ASP.NET Core, Dodaj następujący `<FrameworkReference>` element do pliku projektu:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Odwoływanie się ASP.NET Core w ten sposób jest obsługiwane tylko dla projektów przeznaczonych dla platformy .NET Core 3. x.

## <a name="include-blazor-extensibility"></a>Uwzględnij Blazor rozszerzalność

Blazorobsługuje [modele hostingu](xref:blazor/hosting-models)webassembly (WASM) i serwera. O ile nie ma konkretnego powodu, biblioteka [ Razor składników](xref:blazor/components/index) powinna obsługiwać oba modele hostingu. RazorBiblioteka składników musi korzystać z [zestawu Microsoft. NET. Sdk. Razor Zestaw SDK](xref:razor-pages/sdk).

### <a name="support-both-hosting-models"></a>Obsługa obu modeli hostingu

Aby obsługiwać Razor użycie składników z projektów [ Blazor serwer](xref:blazor/hosting-models#blazor-server) i [ Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) , należy wykonać następujące instrukcje dla edytora.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Użyj szablonu projektu ** Razor Biblioteka klas** . Pole wyboru **strony obsługi i widoki** szablonu powinno zostać odwybrane.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w zintegrowanym terminalu:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Użyj szablonu projektu ** Razor Biblioteka klas** .

---

Projekt wygenerowany na podstawie szablonu wykonuje następujące czynności:

* Elementy docelowe .NET Standard 2,0.
* Ustawia `RazorLangVersion` Właściwość na `3.0` . `3.0`jest wartością domyślną dla platformy .NET Core 3. x.
* Dodaje następujące odwołania do pakietów:
  * [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft. AspNetCore. Components. Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Obsługa określonego modelu hostingu

Jest to bardzo mniej powszechne do obsługi jednego Blazor modelu hostingu. Przykładowo, aby obsługiwać Razor użycie składników tylko z projektów [ Blazor serwera](xref:blazor/hosting-models#blazor-server) :

* Docelowy .NET Core 3. x.
* Dodaj `<FrameworkReference>` element dla struktury udostępnionej.

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Aby uzyskać więcej informacji na temat bibliotek zawierających Razor składniki, zobacz [ASP.NET Core Razor składniki klas](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Uwzględnij rozszerzalność MVC

W tej części przedstawiono zalecenia dotyczące bibliotek, które obejmują:

* [Razorwidoki lub Razor strony](#razor-views-or-razor-pages)
* [Pomocnicy tagów](#tag-helpers)
* [Składniki widoku](#view-components)

Ta sekcja nie omawia wielu elementów docelowych w celu obsługi wielu wersji MVC. Aby uzyskać wskazówki dotyczące obsługi wielu wersji ASP.NET Core, zobacz [Obsługa wielu ASP.NET Core wersji](#support-multiple-aspnet-core-versions).

### <a name="razor-views-or-razor-pages"></a>Razorwidoki lub Razor strony

Projekt, który zawiera [ Razor widoki](xref:mvc/views/overview) lub [ Razor strony](xref:razor-pages/index) , musi korzystać z [zestawu Microsoft. NET. Sdk. Razor Zestaw SDK](xref:razor-pages/sdk).

Jeśli projekt jest przeznaczony dla platformy .NET Core 3. x, wymaga:

* `AddRazorSupportForMvc`Właściwość programu MSBuild ustawiona na wartość `true` .
* `<FrameworkReference>`Element dla struktury udostępnionej.

Szablon projektu ** Razor biblioteki klas** spełnia powyższe wymagania dotyczące projektów przeznaczonych dla platformy .NET Core 3. x. Wykonaj następujące instrukcje dla edytora.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Użyj szablonu projektu ** Razor Biblioteka klas** . Należy zaznaczyć pole wyboru **strony i widoki pomocy technicznej** szablonu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w zintegrowanym terminalu:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

W tej chwili nie ma obsługi szablonu projektu.

---

Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Jeśli obiekt docelowy projektu .NET Standard zamiast tego wymagane jest odwołanie do pakietu [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) . `Microsoft.AspNetCore.Mvc`Pakiet został przeniesiony do udostępnionej struktury w ASP.NET Core 3,0 i dlatego nie jest już opublikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Pomocnicy tagów

Projekt zawierający [pomocników tagów](xref:mvc/views/tag-helpers/intro) powinien korzystać z `Microsoft.NET.Sdk` zestawu SDK. Jeśli celem jest .NET Core 3. x, Dodaj `<FrameworkReference>` element dla struktury udostępnionej. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Jeśli .NET Standard określania wartości docelowej (aby obsługiwać wersje starsze niż ASP.NET Core 3. x), Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. Razor MVC.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor). `Microsoft.AspNetCore.Mvc.Razor`Pakiet został przeniesiony do udostępnionej struktury i w związku z tym nie jest już publikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Składniki widoku

Projekt, który zawiera [składniki widoku](xref:mvc/views/view-components) , powinien korzystać z `Microsoft.NET.Sdk` zestawu SDK. Jeśli celem jest .NET Core 3. x, Dodaj `<FrameworkReference>` element dla struktury udostępnionej. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Jeśli .NET Standard określania wartości docelowej (aby obsługiwać wersje starsze niż ASP.NET Core 3. x), Dodaj odwołanie do pakietu do [Microsoft. AspNetCore. MVC. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures). `Microsoft.AspNetCore.Mvc.ViewFeatures`Pakiet został przeniesiony do udostępnionej struktury i w związku z tym nie jest już publikowany. Przykład:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Obsługa wielu wersji ASP.NET Core

Do utworzenia biblioteki, która obsługuje wiele wariantów ASP.NET Core, wymagany jest wiele elementów docelowych. Rozważmy scenariusz, w którym Biblioteka pomocników tagów musi obsługiwać następujące ASP.NET Core wariantów:

* ASP.NET Core 2,1 .NET Framework 4.6.1
* ASP.NET Core 2. x przeznaczony dla platformy .NET Core 2. x
* ASP.NET Core 3. x przeznaczony dla platformy .NET Core 3. x

Następujący plik projektu obsługuje te warianty za pośrednictwem `TargetFrameworks` Właściwości:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Z poprzednim plikiem projektu:

* `Markdig`Pakiet zostanie dodany dla wszystkich odbiorców.
* Odwołanie do [Microsoft. AspNetCore. MVC. Razor ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) jest dodawany dla klientów docelowych .NET Framework 4.6.1 lub nowszych lub .NET Core 2. x. Wersja 2.1.0 pakietu współpracuje z ASP.NET Core 2,2 ze względu na zgodność z poprzednimi wersjami.
* Udostępnione środowisko jest przywoływane dla odbiorców przeznaczonych dla platformy .NET Core 3. x. `Microsoft.AspNetCore.Mvc.Razor`Pakiet jest dołączony do udostępnionej struktury.

Alternatywnie, .NET Standard 2,0 może być ukierunkowana zamiast określania zarówno platformy .NET Core 2,1, jak i .NET Framework 4.6.1:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

W poprzednim pliku projektu istnieją następujące zastrzeżenia:

* Ponieważ biblioteka zawiera tylko pomocników tagów, jest bardziej prosta dla konkretnych platform, na których ASP.NET Core są uruchamiane: .NET Core i .NET Framework. Pomocnicy tagów nie mogą być używani przez inne platformy docelowe zgodne z .NET Standard 2,0, takie jak Unity, platformy UWP i Xamarin.
* Korzystanie z .NET Standard 2,0 z .NET Framework ma problemy, które zostały rozwiązane w .NET Framework 4.7.2. Możesz ulepszyć środowisko dla klientów, korzystając .NET Framework 4.6.1 przez 4.7.1 przez kierowanie .NET Framework 4.6.1.

Jeśli biblioteka wymaga wywołania interfejsów API specyficznych dla platformy, użyj konkretnych implementacji platformy .NET, a nie .NET Standard. Aby uzyskać więcej informacji, zobacz temat [wiele elementów docelowych](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Użyj interfejsu API, który nie został zmieniony

Wyobraź sobie scenariusz, w którym uaktualniasz bibliotekę oprogramowania pośredniczącego z programu .NET Core 2,2 do 3,0. ASP.NET Core interfejsy API oprogramowania pośredniczącego używane w bibliotece nie uległy zmianie między ASP.NET Core 2,2 i 3,0. Aby nadal obsługiwać bibliotekę oprogramowania pośredniczącego w programie .NET Core 3,0, wykonaj następujące czynności:

* Postępuj zgodnie ze [wskazówkami dotyczącymi biblioteki standardowej](/dotnet/standard/library-guidance/).
* Dodaj odwołanie do pakietu dla każdego pakietu NuGet interfejsu API, jeśli odpowiedni zestaw nie istnieje w udostępnionej platformie.

## <a name="use-an-api-that-changed"></a>Użyj interfejsu API, który został zmieniony

Wyobraź sobie scenariusz, w którym biblioteka jest uaktualniana z platformy .NET Core 2,2 do programu .NET Core 3,0. Interfejs API ASP.NET Core używany w bibliotece ma [nieprzerwaną zmianę](/dotnet/core/compatibility/breaking-changes) w ASP.NET Core 3,0. Rozważ, czy biblioteka może być ponownie zapisywana, aby nie używać uszkodzonego interfejsu API we wszystkich wersjach.

Jeśli możesz ponownie zapisać bibliotekę, zrób to i przejdź do lokalizacji docelowej starszej platformy docelowej (na przykład .NET Standard 2,0 lub .NET Framework 4.6.1) z odwołaniami do pakietu.

Jeśli nie możesz ponownie zapisać biblioteki, wykonaj następujące czynności:

* Dodaj element docelowy dla platformy .NET Core 3,0.
* Dodaj `<FrameworkReference>` element dla struktury udostępnionej.
* Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem platformy docelowej do warunkowego kompilowania kodu.

Na przykład synchroniczne operacje odczytu i zapisu w strumieniach żądań HTTP i odpowiedzi są domyślnie wyłączone w ASP.NET Core 3,0. ASP.NET Core 2,2 domyślnie obsługuje zachowanie synchroniczne. Rozważmy bibliotekę oprogramowania pośredniczącego, w której operacje odczytu i zapisu synchronicznego powinny być włączone w przypadku wystąpienia operacji we/wy. Biblioteka powinna zawierać kod w celu włączenia funkcji synchronicznych w odpowiedniej dyrektywie preprocesora. Przykład:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Korzystanie z interfejsu API wprowadzonego w 3,0

Załóżmy, że chcesz użyć interfejsu API ASP.NET Core, który został wprowadzony w ASP.NET Core 3,0. Zastanów się nad następującymi pytaniami:

1. Czy biblioteka wymaga, aby nowy interfejs API był funkcjonalny?
1. Czy biblioteka może zaimplementować tę funkcję w inny sposób?

Jeśli biblioteka funkcjonalnie wymaga interfejsu API i nie ma możliwości wdrożenia go na poziomie:

* Tylko docelowy .NET Core 3. x.
* Dodaj `<FrameworkReference>` element dla struktury udostępnionej.

Jeśli biblioteka może zaimplementować funkcję w inny sposób:

* Dodaj platformę .NET Core 3. x jako platformę docelową.
* Dodaj `<FrameworkReference>` element dla struktury udostępnionej.
* Użyj [dyrektywy preprocesora #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) z odpowiednim symbolem platformy docelowej do warunkowego kompilowania kodu.

Na przykład poniższy pomocnik tagów używa <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interfejsu wprowadzonego w ASP.NET Core 3,0. Odbiorcy korzystający z platformy .NET Core 3,0 wykonują ścieżkę kodu zdefiniowaną przez `NETCOREAPP3_0` symbol platformy docelowej. Typ parametru konstruktora pomocnika tagów zmieni się na <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dla platformy .NET Core 2,1 i .NET Framework 4.6.1. Ta zmiana była niezbędna, ponieważ ASP.NET Core 3,0 oznaczona `IHostingEnvironment` jako przestarzała i zalecana `IWebHostEnvironment` jako zastąpienie.

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

Następujący plik projektu wielowymiarowego obsługuje ten scenariusz pomocnika tagów:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Użyj interfejsu API usuniętego z udostępnionej platformy

Aby użyć zestawu ASP.NET Core, który został usunięty z platformy udostępnionej, Dodaj odpowiednie odwołanie do pakietu. Aby uzyskać listę pakietów usuniętych z udostępnionej platformy w ASP.NET Core 3,0, zobacz [usuwanie przestarzałych odwołań do pakietów](xref:migration/22-to-30#remove-obsolete-package-references).

Na przykład, aby dodać klienta internetowego interfejsu API:

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
* <xref:blazor/components/class-libraries>
* [Obsługa implementacji platformy .NET](/dotnet/standard/net-standard#net-implementation-support)
* [Zasady pomocy technicznej platformy .NET](https://dotnet.microsoft.com/platform/support/policy)
