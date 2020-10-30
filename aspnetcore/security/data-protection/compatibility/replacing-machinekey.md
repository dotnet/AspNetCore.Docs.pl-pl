---
title: Zastąp ASP.NET machineKey w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak zastąpić machineKey w ASP.NET, aby umożliwić korzystanie z nowego i bezpieczniejszego systemu ochrony danych.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053062"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="0bce8-103">Zastąp ASP.NET machineKey w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0bce8-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="0bce8-104">Implementacja `<machineKey>` elementu w ASP.NET [jest wymienna](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="0bce8-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="0bce8-105">Umożliwia to większości wywołań ASP.NET procedur kryptograficznych, które mają być kierowane przez mechanizm ochrony danych zamiennych, w tym nowy system ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="0bce8-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="0bce8-106">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="0bce8-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="0bce8-107">Nowy system ochrony danych można zainstalować tylko w istniejącej aplikacji ASP.NET, w której znajduje się program .NET 4.5.1 lub nowszy.</span><span class="sxs-lookup"><span data-stu-id="0bce8-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="0bce8-108">Instalacja zakończy się niepowodzeniem, jeśli aplikacja jest przeznaczona dla platformy .NET 4,5 lub niższej.</span><span class="sxs-lookup"><span data-stu-id="0bce8-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="0bce8-109">Aby zainstalować nowy system ochrony danych w istniejącym projekcie ASP.NET 4.5.1 +, zainstaluj pakiet Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="0bce8-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="0bce8-110">Spowoduje to utworzenie wystąpienia systemu ochrony danych przy użyciu [domyślnych ustawień konfiguracji](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="0bce8-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="0bce8-111">Po zainstalowaniu pakietu wstawia wiersz do *Web.config* , który informuje ASP.NET o tym, aby użyć go do [większości operacji kryptograficznych](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), w tym uwierzytelniania formularzy, stanu widoku i wywołań machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="0bce8-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="0bce8-112">Wstawiony wiersz odczytuje się w następujący sposób.</span><span class="sxs-lookup"><span data-stu-id="0bce8-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="0bce8-113">Można sprawdzić, czy nowy system ochrony danych jest aktywny, sprawdzając pola, takie jak `__VIEWSTATE` , które powinny zaczynać się od "CfDJ8", jak w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="0bce8-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="0bce8-114">"CfDJ8" to reprezentacja Base64 nagłówka Magic "09 F0 C9 F0", który identyfikuje ładunek chroniony przez system ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="0bce8-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="0bce8-115">Konfiguracja pakietu</span><span class="sxs-lookup"><span data-stu-id="0bce8-115">Package configuration</span></span>

<span data-ttu-id="0bce8-116">System ochrony danych jest inicjowany z domyślną konfiguracją instalacji zerowej.</span><span class="sxs-lookup"><span data-stu-id="0bce8-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="0bce8-117">Ponieważ jednak domyślnie klucze są utrwalane w lokalnym systemie plików, nie będzie to konieczne w przypadku aplikacji wdrożonych w farmie.</span><span class="sxs-lookup"><span data-stu-id="0bce8-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="0bce8-118">Aby rozwiązać ten problem, można zapewnić konfigurację, tworząc typ, który podklasy DataProtectionStartup i przesłania metodę ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="0bce8-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="0bce8-119">Poniżej znajduje się przykład niestandardowego typu uruchamiania ochrony danych, który został skonfigurowany zarówno w przypadku, gdy klucze są utrwalane, jak i w sposób szyfrowany.</span><span class="sxs-lookup"><span data-stu-id="0bce8-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="0bce8-120">Zastępuje ono również domyślne zasady izolacji aplikacji, podając własną nazwę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0bce8-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="0bce8-121">Można również użyć `<machineKey applicationName="my-app" ... />` zamiast jawnie wywołania Setapplicationname.</span><span class="sxs-lookup"><span data-stu-id="0bce8-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="0bce8-122">Jest to wygodny mechanizm pozwalający uniknąć wymuszania tworzenia przez dewelopera typu DataProtectionStartup-pochodnego, jeśli wszystko, co chciało skonfigurować, została ustawiona nazwa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0bce8-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="0bce8-123">Aby włączyć tę konfigurację niestandardową, Wróć do Web.config i poszukaj `<appSettings>` elementu, który został dodany do pliku konfiguracji przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="0bce8-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="0bce8-124">Będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="0bce8-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="0bce8-125">Wypełnij wartość pustą, podając kwalifikowaną nazwę zestawu DataProtectionStartup typu pochodnego.</span><span class="sxs-lookup"><span data-stu-id="0bce8-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="0bce8-126">Jeśli nazwa aplikacji jest DataProtectionDemo, będzie wyglądać tak jak poniżej.</span><span class="sxs-lookup"><span data-stu-id="0bce8-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="0bce8-127">Nowo skonfigurowany system ochrony danych jest teraz gotowy do użycia w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0bce8-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
