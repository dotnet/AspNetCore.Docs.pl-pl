---
title: RazorBiblioteki klas składników ASP.NET Core
author: guardrex
description: Odkryj, jak składniki mogą być dołączane do Blazor aplikacji z zewnętrznej biblioteki składników.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: ecc9873d7f652f27767df98196786d12789518c9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103899"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>RazorBiblioteki klas składników ASP.NET Core

Autor [Simon Timms](https://github.com/stimms)

Składniki mogą być współużytkowane w [ Razor bibliotece klas (RCL)](xref:razor-pages/ui-class) w różnych projektach. * Razor Biblioteka klas składników* może być dołączana z:

* Inny projekt w rozwiązaniu.
* Pakiet NuGet.
* Przywoływana Biblioteka platformy .NET.

Podobnie jak składniki są zwykłymi typami .NET, składniki udostępniane przez RCL są normalnymi zestawami platformy .NET.

## <a name="create-an-rcl"></a>Utwórz RCL

Postępuj zgodnie ze wskazówkami zawartymi w <xref:blazor/get-started> artykule, aby skonfigurować środowisko dla programu Blazor .

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.
1. Wybierz ** Razor bibliotekę klas**. Wybierz przycisk **Dalej**.
1. W oknie dialogowym **Utwórz nową Razor bibliotekę klas** wybierz pozycję **Utwórz**.
1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. W przykładach w tym temacie użyto nazwy projektu `MyComponentLib1` . Wybierz przycisk **Utwórz**.
1. Dodaj RCL do rozwiązania:
   1. Kliknij prawym przyciskiem myszy rozwiązanie. Wybierz pozycję **Dodaj**  >  **istniejący projekt**.
   1. Przejdź do pliku projektu RCL.
   1. Wybierz plik projektu RCL (*. csproj*).
1. Dodaj odwołanie RCL z aplikacji:
   1. Kliknij prawym przyciskiem myszy projekt aplikacji. Wybierz pozycję **Dodaj**  >  **odwołanie**.
   1. Wybierz projekt RCL. Wybierz przycisk **OK**.

> [!NOTE]
> Jeśli pole wyboru **strony i widoki pomocy technicznej** jest zaznaczone podczas generowania RCL z szablonu, Dodaj również plik *_Imports. Razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

1. Użyj szablonu ** Razor biblioteki klas** ( `razorclasslib` ) z poleceniem [dotnet New](/dotnet/core/tools/dotnet-new) w powłoce poleceń. W poniższym przykładzie jest tworzony RCL o nazwie `MyComponentLib1` . Folder, który `MyComponentLib1` ma zostać utworzony, jest tworzony automatycznie podczas wykonywania polecenia:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Jeśli `-s|--support-pages-and-views` przełącznik jest używany podczas generowania RCL z szablonu, Dodaj również plik *_Imports. Razor* do katalogu głównego wygenerowanego projektu z następującą zawartością, aby włączyć Razor Tworzenie składników:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ręcznie Dodaj plik do katalogu głównego wygenerowanego projektu.

1. Aby dodać bibliotekę do istniejącego projektu, użyj polecenia [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) w powłoce poleceń. W poniższym przykładzie RCL jest dodawany do aplikacji. Wykonaj następujące polecenie z folderu projektu aplikacji z ścieżką do biblioteki:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Korzystanie ze składnika biblioteki

Aby można było korzystać ze składników zdefiniowanych w bibliotece w innym projekcie, należy użyć jednej z następujących metod:

* Użyj pełnej nazwy typu z przestrzeni nazw.
* Zastosuj Razor [`@using`](xref:mvc/views/razor#using) dyrektywę. Poszczególne składniki można dodawać według nazwy.

W poniższych przykładach `MyComponentLib1` jest biblioteka składników zawierająca `SalesReport` składnik.

Do `SalesReport` składnika można odwoływać się za pomocą jego pełnej nazwy typu z przestrzenią nazw:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Składnik może być również przywoływany, jeśli biblioteka została wprowadzona do zakresu przy użyciu `@using` dyrektywy:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Uwzględnij `@using MyComponentLib1` dyrektywę w pliku *_Import. Razor* najwyższego poziomu, aby udostępnić składniki biblioteki dla całego projektu. Dodaj dyrektywę do pliku *_Import. Razor* na dowolnym poziomie, aby zastosować przestrzeń nazw do pojedynczej strony lub zestawu stron w folderze.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Tworzenie Razor biblioteki klas składników ze statycznymi zasobami

RCL może zawierać statyczne zasoby. Zasoby statyczne są dostępne dla każdej aplikacji, która korzysta z biblioteki. Aby uzyskać więcej informacji, zobacz <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Kompilowanie, pakowanie i dostarczanie do narzędzia NuGet

Ponieważ biblioteki składników są standardowymi bibliotekami .NET, pakowanie i dostarczanie ich do narzędzia NuGet nie różni się od pakowania i wysyłania żadnej biblioteki do narzędzia NuGet. Pakowanie jest wykonywane przy użyciu polecenia [pakietu dotnet](/dotnet/core/tools/dotnet-pack) w powłoce poleceń:

```dotnetcli
dotnet pack
```

Przekaż pakiet do narzędzia NuGet przy użyciu polecenia [push NuGet w trybie wypychania](/dotnet/core/tools/dotnet-nuget-push) w powłoce poleceń.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:razor-pages/ui-class>
* [Dodawanie pliku konfiguracji konsolidatora XML do biblioteki](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
