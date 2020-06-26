---
title: Pomocnik tagu obrazu w ASP.NET Core
author: pkellner
description: Pokazuje, jak korzystać z pomocnika tagów obrazu.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 4d8981b8bdf2e1341b5943745b901efd867d2e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399364"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Pomocnik tagu obrazu w ASP.NET Core

Według [Peterowi Kellner](https://peterkellner.net)

Pomocnik tagu obrazu ulepsza tag, `<img>` Aby zapewnić zachowanie Busting pamięci podręcznej dla statycznych plików obrazu.

Busting pamięci podręcznej jest unikatową wartością reprezentującą skrót pliku obrazu statycznego dołączonego do adresu URL zasobu. Unikatowy ciąg będzie monitował klientów (i niektórych serwerów proxy) do ponownego załadowania obrazu z serwera hosta sieci Web, a nie z pamięci podręcznej klienta.

Jeśli źródło obrazu ( `src` ) jest plikiem statycznym na serwerze sieci Web hosta:

* Unikatowy ciąg Busting jest dołączany jako parametr zapytania do źródła obrazu.
* Jeśli plik na serwerze sieci Web hosta ulegnie zmianie, generowany jest unikatowy adres URL żądania, który obejmuje zaktualizowany parametr żądania.

Aby zapoznać się z omówieniem pomocników tagów, zobacz <xref:mvc/views/tag-helpers/intro> .

## <a name="image-tag-helper-attributes"></a>Atrybuty pomocnika tagów obrazu

### <a name="src"></a>src

Aby uaktywnić pomocnika tagów obrazu, `src` atrybut jest wymagany w `<img>` elemencie.

Źródło obrazu ( `src` ) musi wskazywać fizyczny plik statyczny na serwerze. Jeśli `src` jest to zdalny identyfikator URI, parametr ciągu zapytania cache-Busting nie jest generowany.

### <a name="asp-append-version"></a>ASP — dołączanie wersji

Gdy `asp-append-version` jest określony z `true` wartością wraz z `src` atrybutem, zostanie wywołana pomocnika znacznika obrazu.

Poniższy przykład używa pomocnika tagu obrazu:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Jeśli plik statyczny istnieje w katalogu */wwwroot/images/*, wygenerowany kod HTML jest podobny do następującego (skrót będzie różny):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Wartość przypisana do parametru `v` jest wartością skrótu pliku *asplogo.png* na dysku. Jeśli serwer sieci Web nie może uzyskać dostępu do odczytu do pliku statycznego, żaden `v` parametr nie zostanie dodany do `src` atrybutu w renderowanej adjustacji.

## <a name="hash-caching-behavior"></a>Zachowanie buforowania wartości skrótu

Pomocnik tagu obrazu używa dostawcy pamięci podręcznej na lokalnym serwerze sieci Web do przechowywania obliczonego `Sha512` skrótu danego pliku. Jeśli plik jest żądany wielokrotnie, skrót nie jest obliczany ponownie. Pamięć podręczna jest unieważniona przez obserwatora plików, który jest dołączony do pliku po `Sha512` obliczeniu skrótu pliku. Gdy plik zostanie zmieniony na dysku, zostanie obliczony i zbuforowany nowy skrót.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:performance/caching/memory>
