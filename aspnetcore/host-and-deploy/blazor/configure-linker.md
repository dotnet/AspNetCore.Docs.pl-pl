---
title: Konfigurowanie konsolidatora dla ASP.NET CoreBlazor
author: guardrex
description: Dowiedz się, jak sterować konsolidatorem języka Blazor pośredniego podczas tworzenia aplikacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218664"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Konfigurowanie konsolidatora dla ASP.NET Core Blazor

Przez [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly wykonuje [intermediate language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) łączenie podczas kompilacji, aby przyciąć niepotrzebne IL z zestawów wyjściowych aplikacji. Konsolidator jest wyłączony podczas tworzenia w konfiguracji debugowania. Aplikacje muszą tworzyć w konfiguracji wersji, aby włączyć konsolidator. Zalecamy tworzenie w wersji podczas wdrażania aplikacji Blazor WebAssembly. 

Łączenie aplikacji optymalizuje rozmiar, ale może mieć szkodliwe skutki. Aplikacje, które używają odbicia lub powiązanych funkcji dynamicznych może pęknąć po przycięciu, ponieważ konsolidator nie wie o tym dynamicznym zachowaniu i nie można określić ogólnie, które typy są wymagane do odbicia w czasie wykonywania. Aby przyciąć takie aplikacje, konsolidator musi być informowany o wszystkich typach wymaganych przez odbicie w kodzie i w pakietach lub strukturach, od których zależy aplikacja. 

Aby upewnić się, że przycięta aplikacja działa poprawnie po wdrożeniu, ważne jest, aby testować kompilacje wydania aplikacji często podczas tworzenia.

Łączenie dla aplikacji Blazor można skonfigurować przy użyciu następujących funkcji MSBuild:

* Konfigurowanie łączenia globalnie z [właściwością MSBuild](#control-linking-with-an-msbuild-property).
* Sterowanie łączeniem na podstawie na zespół z [plikiem konfiguracyjnym](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Łączenie formantów z właściwością MSBuild

Łączenie jest włączone, gdy aplikacja `Release` jest wbudowana w konfigurację. Aby to zmienić, `BlazorWebAssemblyEnableLinking` skonfiguruj właściwość MSBuild w pliku projektu:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Sterowanie łączem z plikiem konfiguracyjnym

Kontroluj łączenie na podstawie naze zespołu, udostępniając plik konfiguracyjny XML i określając plik jako element MSBuild w pliku projektu:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

Aby uzyskać więcej informacji, zobacz [Łączenie przykładów plików xml (repozytorium GitHub mono/linker).](https://github.com/mono/linker#link-xml-file-examples)

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Dodawanie pliku konfiguracyjnego konsolidatora XML do biblioteki

Aby skonfigurować konsolidator dla określonej biblioteki, dodaj plik konfiguracyjny konsolidatora XML do biblioteki jako zasób osadzony. Osadzony zasób musi mieć taką samą nazwę jak zestaw.

W poniższym przykładzie plik *LinkerConfig.xml* jest określony jako osadzony zasób, który ma taką samą nazwę jak zestaw biblioteki:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Konfigurowanie konsolidatora do internacjonalizacji

Domyślnie konfiguracja konsolidatora Blazora dla aplikacji Blazor WebAssembly usuwa informacje o internacjonalizacji, z wyjątkiem wyraźnie żądanych ustawień regionalnych. Usunięcie tych zestawów minimalizuje rozmiar aplikacji.

Aby kontrolować, które zestawy I18N są `<MonoLinkerI18NAssemblies>` zachowywane, ustaw właściwość MSBuild w pliku projektu:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Wartość regionu     | Montaż regionu mono    |
| ---------------- | ----------------------- |
| `all`            | Wszystkie zestawy w zestawie |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`(domyślnie) | Brak                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Użyj przecinka, aby oddzielić `mideast,west`wiele wartości (na przykład ).

Aby uzyskać więcej informacji, zobacz [I18N: Pnetlib Internationalization Framework Library (mon/mono Repozytorium GitHub).](https://github.com/mono/mono/tree/master/mcs/class/I18N)
