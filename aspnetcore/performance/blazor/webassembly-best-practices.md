---
title: ASP.NET Core Blazor najlepszych rozwiązań dotyczących wydajności zestawu Webassembly
author: pranavkm
description: Wskazówki dotyczące zwiększania wydajności w ASP.NET Core Blazor aplikacjach webassembly i unikania typowych problemów z wydajnością.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 9e9b166cb9ce9870a8ff275b72bb12f04b84751b
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2020
ms.locfileid: "83439438"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor najlepszych rozwiązań dotyczących wydajności zestawu Webassembly

Autor [Pranav Krishnamoorthy](https://github.com/pranavkm)

Ten artykuł zawiera wskazówki dotyczące ASP.NET Core najlepszych rozwiązań w zakresie Blazor wydajności zestawu webassembly.

## <a name="avoid-unnecessary-component-renders"></a>Unikaj renderowania zbędnych składników

Blazoralgorytm różnicowania unika odrenderowania składnika, gdy algorytm postrzega, że składnik nie został zmieniony. Przesłoń [ComponentBase. ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) w celu uzyskania szczegółowej kontroli nad renderowaniem składników.

W przypadku tworzenia składnika tylko interfejsu użytkownika, który nigdy nie zmienia się po początkowym renderowaniu, skonfiguruj `ShouldRender` do zwrócenia `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Większość aplikacji nie wymaga precyzyjnej kontroli, ale <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> może być również używana do selektywnego renderowania składnika odpowiadającego zdarzeniu interfejsu użytkownika.

W poniższym przykładzie:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>jest zastępowany i ustawiany na wartość `shouldRender` pola, początkowo `false` podczas ładowania składnika.
* Gdy przycisk jest zaznaczony, `shouldRender` jest ustawiony na `true` , co wymusza ponowną renderowanie składnika przy użyciu zaktualizowanego `currentCount` .
* Natychmiast po <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> ponownym renderowaniu ustawia wartość z powrotem na, aby zapobiec dalszemu ponownemu przywracaniu do `shouldRender` `false` momentu, gdy przycisk zostanie wybrany.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Wirtualizacja fragmentów do ponownego użycia

Składniki oferują wygodną metodę tworzenia fragmentów kodu i znaczników do ponownego użycia. Ogólnie rzecz biorąc zalecamy tworzenie pojedynczych składników, które najlepiej dopasowują się do wymagań aplikacji. Jednym z zawartoocią jest to, że każdy dodatkowy składnik podrzędny wnosi do łącznego czasu potrzebnego na renderowanie składnika nadrzędnego. W przypadku większości aplikacji dodatkowe obciążenie jest znikome. W przypadku aplikacji, które tworzą dużą liczbę składników, należy rozważyć użycie strategii w celu zmniejszenia nakładu pracy, np. ograniczenia liczby renderowanych składników.

Na przykład siatka lub lista, która renderuje setki wierszy zawierających składniki, ma intensywną obsługę procesora. Rozważ wirtualizację układu siatki lub listy tak, że tylko podzestaw składników jest renderowany w danym momencie. Aby zapoznać się z przykładem renderowania podzestawu składników, zobacz następujące składniki w [przykładowej aplikacji wirtualizacji](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`składnik ([Shared/wirtualizacja. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): składnik zapisany w języku C#, który implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> zestaw wierszy danych pogody na podstawie przewijania przez użytkownika.
* `FetchData`składnik ([strony/FetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): używa `Virtualize` składnika do wyświetlania 25 wierszy danych pogody jednocześnie.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Unikaj międzyoperacyjności JavaScript do organizowania danych

W elemencie Blazor webassembly wywołanie międzyoperacyjne języka JavaScript (js) musi przejść do granicy webassembly-js. Serializacja i deserializacja zawartości w dwóch kontekstach powoduje utworzenie obciążenia dla aplikacji. Częste wywołania w programie JS Interop często mają negatywny wpływ na wydajność. Aby zmniejszyć kierowanie danych między granicami, ustal, czy aplikacja może skonsolidować wiele małych ładunków do pojedynczego dużego ładunku, aby uniknąć dużej liczby przełączeń kontekstu między zestawem webassembly i JS.

## <a name="use-systemtextjson"></a>Użyj elementu System. Text. JSON

Blazorimplementacja elementu webinterop w języku JS polega na tym <xref:System.Text.Json> , że jest to biblioteka serializacji JSON o wysokiej wydajności z alokacją małej ilości pamięci. Użycie nie powoduje, że <xref:System.Text.Json> rozmiar ładunku aplikacji jest większy niż dodanie co najmniej jednej alternatywnej biblioteki JSON.

Aby uzyskać wskazówki dotyczące migracji, zobacz [Jak przeprowadzić migrację z Newtonsoft. JSON do System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Używaj synchronicznych i nieskierowanych interfejsów API międzyoperacyjności w razie potrzeby

BlazorZestaw webassembly oferuje dwie dodatkowe wersje programu <xref:Microsoft.JSInterop.IJSRuntime> niż jedna wersja dostępna dla Blazor aplikacji serwerowych:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>umożliwia Asynchroniczne wywoływanie wywołań programu JS Interop, które ma mniej obciążenia niż wersje asynchroniczne:

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>zezwala na nieskierowanie wywołań międzyoperacyjnych JS:

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > Podczas używania <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> ma najmniejsze obciążenie związane z interfejsem js międzyoperacyjności, interfejsy API języka JavaScript wymagane do współdziałania z tymi interfejsy API są obecnie nieudokumentowane i mogą ulegać zmianom w przyszłych wersjach.

## <a name="reduce-app-size"></a>Zmniejsz rozmiar aplikacji

### <a name="intermediate-language-il-linking"></a>Tworzenie łączy języka pośredniego (IL)

[Łączenie z Blazor Aplikacja webassembly](xref:host-and-deploy/blazor/configure-linker) zmniejsza rozmiar aplikacji przez przycinanie nieużywanego kodu w plikach binarnych aplikacji. Domyślnie konsolidator jest włączony tylko w przypadku kompilowania w `Release` konfiguracji. Aby z tego skorzystać, Opublikuj aplikację do wdrożenia za pomocą polecenia [dotnet Publish](/dotnet/core/tools/dotnet-publish) z opcją [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) ustawioną na `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a>Wyłącz nieużywane funkcje

BlazorŚrodowisko uruchomieniowe webassembly obejmuje następujące funkcje platformy .NET, które można wyłączyć, jeśli aplikacja nie wymaga ich dla mniejszego rozmiaru ładunku:

* Plik danych jest uwzględniany w celu poprawnego wprowadzania informacji o strefie czasowej. Jeśli aplikacja nie wymaga tej funkcji, rozważ wyłączenie jej przez ustawienie właściwości programu `BlazorEnableTimeZoneSupport` MSBuild w pliku projektu aplikacji na `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Informacje o sortowaniu są uwzględniane w celu <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> poprawnego działania interfejsów API. Jeśli masz pewność, że aplikacja nie wymaga danych sortowania, rozważ wyłączenie jej przez ustawienie `BlazorWebAssemblyPreserveCollationData` Właściwości programu MSBuild w pliku projektu aplikacji na `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
