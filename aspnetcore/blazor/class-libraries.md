---
title: ASP.NET biblioteki klas składników Razor Core
author: guardrex
description: Dowiedz się, jak składniki Blazor mogą być uwzględniane w aplikacjach z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218769"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET biblioteki klas składników Razor Core

Przez [Simon Timms](https://github.com/stimms)

Składniki mogą być współużytkowane w [bibliotece klas Razor (RCL)](xref:razor-pages/ui-class) między projektami. *Biblioteka klas komponentów razor* może być dołączona z:

* Kolejny projekt w rozwiązaniu.
* Pakiet NuGet.
* Dodowano do biblioteki .NET.

Podobnie jak składniki są zwykłymi typami .NET, komponenty dostarczane przez rcl są normalnymi złożeniami .NET.

## <a name="create-an-rcl"></a>Tworzenie listy RCL

Postępuj zgodnie <xref:blazor/get-started> ze wskazówkami w artykule, aby skonfigurować środowisko dla Blazor.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.
1. Wybierz **bibliotekę klas Razor**. Wybierz **pozycję Dalej**.
1. W oknie **dialogowym Tworzenie nowej biblioteki klas Razor** wybierz pozycję **Utwórz**.
1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu. W przykładach w tym temacie `MyComponentLib1`użyto nazwy projektu . Wybierz **pozycję Utwórz**.
1. Dodaj RCL do rozwiązania:
   1. Kliknij prawym przyciskiem myszy rozwiązanie. Wybierz **pozycję Dodaj** > **istniejący projekt**.
   1. Przejdź do pliku projektu RCL.
   1. Wybierz plik projektu RCL (*.csproj*).
1. Dodaj odwołanie do listy RCL z aplikacji:
   1. Kliknij prawym przyciskiem myszy projekt aplikacji. Wybierz **pozycję Dodaj** > **odniesienie**.
   1. Wybierz projekt RCL. Kliknij przycisk **OK**.

> [!NOTE]
> Jeśli podczas generowania listy RCL z szablonu jest zaznaczone pole wyboru **Strony pomocy technicznej i widoki,** dodaj również plik *_Imports.razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby umożliwić tworzenie komponentów Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ręcznie dodaj plik główny wygenerowanego projektu.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

1. Użyj szablonu **Biblioteka klas Razor** (`razorclasslib`) z nowym poleceniem [dotnet](/dotnet/core/tools/dotnet-new) w powłoce poleceń. W poniższym przykładzie tworzony jest `MyComponentLib1`rcl o nazwie . Folder, który `MyComponentLib1` posiada jest tworzony automatycznie po wykonaniu polecenia:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, a następnie również dodać plik *_Imports.razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby umożliwić tworzenie komponentów Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ręcznie dodaj plik główny wygenerowanego projektu.

1. Aby dodać bibliotekę do istniejącego projektu, użyj polecenia [dotnet dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference) w powłoce poleceń. W poniższym przykładzie RCL jest dodawany do aplikacji. Wykonaj następujące polecenie z folderu projektu aplikacji ze ścieżką do biblioteki:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Korzystanie ze składnika biblioteki

Aby korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:

* Użyj pełnej nazwy typu z obszarem nazw.
* Użyj dyrektywy Razor [ \@przy użyciu.](xref:mvc/views/razor#using) Poszczególne komponenty można dodawać według nazwy.

W poniższych przykładach `MyComponentLib1` jest biblioteka `SalesReport` składników zawierająca składnik.

Do `SalesReport` składnika można odwoływać się przy użyciu jego pełnej nazwy typu z obszarem nazw:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Składnik można również odwoływać się, jeśli biblioteka `@using` jest wniesiona do zakresu z dyrektywą:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Dołącz `@using MyComponentLib1` dyrektywę do pliku *_Import.brzytwa* najwyższego poziomu, aby udostępnić składniki biblioteki całemu projektowi. Dodaj dyrektywę do pliku *_Import.razor* na dowolnym poziomie, aby zastosować obszar nazw do pojedynczej strony lub zestawu stron w folderze.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Tworzenie biblioteki klas komponentów Razor z zasobami statycznymi

Lista RCL może zawierać zasoby statyczne. Zasoby statyczne są dostępne dla każdej aplikacji, która zużywa bibliotekę. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Buduj, pakuj i wysyłaj do NuGet

Ponieważ biblioteki składników są standardowe biblioteki .NET, pakowania i wysyłania ich do NuGet nie różni się od pakowania i wysyłki dowolnej biblioteki do NuGet. Pakowanie odbywa się za pomocą polecenia [dotnet pack](/dotnet/core/tools/dotnet-pack) w powłoce polecenia:

```dotnetcli
dotnet pack
```

Przekaż pakiet do NuGet przy użyciu polecenia [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) w powłoce poleceń.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:razor-pages/ui-class>
* [Dodawanie pliku konfiguracyjnego konsolidatora XML do biblioteki](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
