---
title: Skonfiguruj konsolidator dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) Blazor podczas kompilowania aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b274752b375f68cca0c0a9adf9c146bc525d9eba
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775183"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Skonfiguruj konsolidator dla ASP.NET Core Blazor

Autor [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor webassembly wykonuje konsolidację [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) podczas kompilacji, aby przyciąć niepotrzebny kod IL z zestawów wyjściowych aplikacji. Konsolidator jest wyłączony podczas kompilowania w konfiguracji debugowania. Aplikacje muszą kompilować w konfiguracji wydania, aby umożliwić konsolidator. Zalecamy Kompilowanie w wersji podczas wdrażania aplikacji webassembly Blazor. 

Łączenie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki. Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ konsolidator nie wie o tym zachowaniu dynamicznym i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania. Aby przyciąć takie aplikacje, konsolidator musi być informowany o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja. 

Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby przetestować kompilacje wydań aplikacji często podczas opracowywania.

Łączenie aplikacji Blazor można skonfigurować za pomocą tych funkcji MSBuild:

* Skonfiguruj konsolidację globalnie za pomocą [Właściwości programu MSBuild](#control-linking-with-an-msbuild-property).
* Kontrolowanie łączenia poszczególnych zestawów z [plikiem konfiguracyjnym](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Sterowanie łączeniem z właściwością programu MSBuild

Łączenie jest włączone, gdy aplikacja jest wbudowana `Release` w konfigurację. Aby to zmienić, skonfiguruj Właściwość `BlazorWebAssemblyEnableLinking` programu MSBuild w pliku projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Kontrola łączenia z plikiem konfiguracji

Kontroluj łączenie dla poszczególnych zestawów, dostarczając plik konfiguracyjny XML i określając plik jako element programu MSBuild w pliku projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig. XML*:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Aby uzyskać więcej informacji i przykładów, zobacz [formaty danych (repozytorium w serwisie GitHub/konsolidator)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Dodawanie pliku konfiguracji konsolidatora XML do biblioteki

Aby skonfigurować konsolidator dla określonej biblioteki, Dodaj plik konfiguracji konsolidatora XML do biblioteki jako zasób osadzony. Osadzony zasób musi mieć taką samą nazwę jak zestaw.

W poniższym przykładzie plik *LinkerConfig. XML* jest określony jako zasób osadzony, który ma taką samą nazwę jak zestaw biblioteki:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Konfigurowanie konsolidatora dla celów wielojęzycznych

Domyślnie konfiguracja konsolidatora Blazor dla aplikacji Blazor webassembly umożliwia rozłączenie informacji o danych wielojęzycznych z wyjątkiem lokalizacji lokalnych jawnie żądanych. Usunięcie tych zestawów minimalizuje rozmiar aplikacji.

Aby kontrolować, które zestawy I18N są zachowywane, `<BlazorWebAssemblyI18NAssemblies>` ustaw właściwość programu MSBuild w pliku projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Wartość regionu     | Zestaw regionów mono    |
| ---------------- | ----------------------- |
| `all`            | Uwzględnione wszystkie zestawy |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`wartooć | Brak                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Użyj przecinka, aby rozdzielić wiele wartości (na `mideast,west`przykład).

Aby uzyskać więcej informacji, zobacz [i18n: Pnetlib — Biblioteka struktury wielojęzycznej (repozytorium usługi GitHub/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
